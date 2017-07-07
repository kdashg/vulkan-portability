## Glossary
- Instance (VkInstance, IDXGIFactory, -)
- Adapter (VkPhysicalDevice, IDXGIAdapter, -)
- Device (VkDevice, ID3D12Device, MTLDevice)
- Command Queue {VkQueue, ID3D12CommandQueue, MTLCommandQueue}
- Command Buffer (VkCommandBuffer, ID3D12CommandList, MTLCommandBuffer)
- Render Pass (VkRenderPass, -, MTLRenderCommandEncoder)



# Queues
Command Queue objects exist in all three APIs: VkQueue, ID3D12CommandQueue, and MTLCommandQueue.
Command Queues are specific to Contexts.
Command Buffers are submitted via Command Queues.
In Metal, Command Buffers are created from Command Queues, whereas in Vulkan and D3D12 they are created from Devices, and submitted to a same-Device Command Queue later.

While queues in D3D12 and Metal are created as-needed, queues in Vulkan are created at during device creation.
vkCreateDevice takes an array of VkDeviceQueueCreateInfos, which respectively specify the number of queues to create for each "queue family".
Queue families are enumerated via  vkGetPhysicalDeviceQueueFamilyProperties(VkPhysicalDevice), which details the VkQueueFlags supported by that queue family.
A device's queues are then retrieved with vkGetDeviceQueue(device, queueFamilyIndex, queueIndex).

## Command Queue types
There are three types of commands in each API:
- Graphics (Graphics, Direct, Render)
- Compute
- Transfer (Transfer, Copy, Blit)

Compute and Transfer don't seem to be fleshed out in D3D12 yet? (ID3D12GraphicsCommandList is the only documented child of ID3D12CommandList)

### D3D12
Each ID3D12CommandQueue has a single D3D12_COMMAND_LIST_TYPE:

- D3D12_COMMAND_LIST_TYPE_DIRECT  (Graphics+Compute+Transfer)
- D3D12_COMMAND_LIST_TYPE_COMPUTE (Compute+Transfer)
- D3D12_COMMAND_LIST_TYPE_COPY    (Transfer)

### Vulkan
Each VkQueue may support an or'd combination of flags (VkQueueFlags) in VkQueueFamilyProperties::queueFlags:

- VK_QUEUE_GRAPHICS_BIT
- VK_QUEUE_COMPUTE_BIT
- VK_QUEUE_TRANSFER_BIT

Very relevant:
"If an implementation exposes any queue family that supports graphics operations, at least one queue family of at least one physical device exposed by the implementation must support both graphics and compute operations."

"All commands that are allowed on a queue that supports transfer operations are also allowed on a queue that supports either graphics or compute operations. Thus, if the capabilities of a queue family include VK_QUEUE_GRAPHICS_BIT or VK_QUEUE_COMPUTE_BIT, then reporting the VK_QUEUE_TRANSFER_BIT capability separately for that queue family is optional."

### Metal
The command types are surfaced as three distinct MTL*CommandEncoder interfaces, created from the following MTLCommandBuffer methods:

- makeRenderCommandEncoder()  -> MTLRenderCommandEncoder  (Graphics)
- makeComputeCommandEncoder() -> MTLComputeCommandEncoder
- makeBlitCommandEncoder()    -> MTLBlitCommandEncoder    (Transfer)






# Command Buffers
Due to the differences in command buffer submission for Metal, I'll delve into Command Buffers a bit.

## Command Buffer creation

- vkAllocateCommandBuffers(VkCommandPool)
- ID3D12Device::CreateCommandList(D3D12_COMMAND_LIST_TYPE, ID3D12CommandAllocator)
- MTLCommandQueue::makeCommandBuffer()

## Command Buffer recording
Begin:

- vkBeginCommandBuffer(VkCommandBuffer)
- (implicit with CreateCommandList)
- (implicit with makeCommandBuffer)

End:

- vkBeginCommandBuffer(VkCommandBuffer)
- ID3D12GraphicsCommandList::Close()
- (implicit with makeCommandBuffer)

Reset:

- vkResetCommandBuffer(VkCommandBuffer)
- ID3D12GraphicsCommandList::Reset()
- (unsupported)

## Command Buffer submission
- vkQueueSubmit(VkQueue, VkSubmitInfo{ VkCommandBuffer[] }[])
- ID3D12CommandQueue::ExecuteCommandLists(ID3D12CommandList[])
- MTLCommandBuffer::enqueue()



## Rough skeletons:
Vulkan:

~~~
device = vkCreateDevice(VkInstance, VkDeviceQueueCreateInfo[])
commandQueue = vkGetDeviceQueue(device)
commandBuffer = vkAllocateCommandBuffers(device)
// ...
vkResetCommandBuffer(commandBuffer)
vkBeginCommandBuffer(commandBuffer
    vkCmdBeginRenderPass(commandBuffer, VkRenderPass, VkFramebuffer)
        vkCmdDraw(commandBuffer)
    vkCmdEndRenderPass(commandBuffer)
vkEndCommandBuffer(commandBuffer)
vkQueueSubmit(commandQueue, commandBuffer)
~~~

D3D12:

~~~
commandQueue = device.ID3D12Device::CreateCommandQueue()
commandBuffer = device.ID3D12Device::CreateCommandList()
// ...
commandBuffer.ID3D12GraphicsCommandList::Reset()
    // implicit Render Pass
        commandBuffer.ID3D12GraphicsCommandList::DrawInstanced()
commandQueue.ID3D12CommandQueue::ExecuteCommandLists(commandBuffer)
~~~

Metal:

~~~
commandQueue = device.MTLDevice::makeCommandQueue()
// ...
commandBuffer = commandQueue.MTLCommandQueue::makeCommandBuffer()
    // potentially commandBuffer.MTLCommandBuffer::enqueue() already
    renderPass = commandBuffer.MTLCommandBuffer::makeRenderCommandEncoder()
        renderPass.MTLRenderCommandEncoder::drawPrimitives()
    renderPass.MTLCommandEncoder::endEncoding()
commandBuffer.MTLCommandBuffer::commit()
~~~

# Fences

## Vulkan
### VkFence
Signals host from GPU.

"Fences are a synchronization primitive that can be used to insert a dependency from a queue to the host. Fences have two states - signaled and unsignaled. A fence can be signaled as part of the execution of a queue submission command. Fences can be unsignaled on the host with vkResetFences. Fences can be waited on by the host with the vkWaitForFences command, and the current state can be queried with vkGetFenceStatus."

### VkSemaphore
Signals command buffer from command buffer.

"Semaphores are a synchronization primitive that can be used to insert a dependency between batches submitted to queues. Semaphores have two states - signaled and unsignaled. The state of a semaphore can be signaled after execution of a batch of commands is completed. A batch can wait for a semaphore to become signaled before it begins execution, and the semaphore is also unsignaled before the batch begins execution."

### VkEvent
Signals queue from queue or host.

"Events are a synchronization primitive that can be used to insert a fine-grained dependency between commands submitted to the same queue, or between the host and a queue. Events have two states - signaled and unsignaled. An application can signal an event, or unsignal it, on either the host or the device. A device can wait for an event to become signaled before executing further operations. No command exists to wait for an event to become signaled on the host, but the current state of an event can be queried."


## D3D12
### ID3D12Fence

- Signals host or GPU from GPU
  - Set to a value with Signal(u64)
  - Polled with GetCompletedValue()->u64
- ID3D12Fence::SetEventOnCompletion(UINT64 Value, HANDLE hEvent)
- ID3D12Device1::SetEventOnMultipleFenceCompletion(ID3D12Fence[] fences, UINT64[] vals, HANDLE hEvent)
- ID3D12CommandQueue::Signal(ID3D12Fence, u64)
- ID3D12CommandQueue::Wait(ID3D12Fence, u64)

### Windows Event
D3D12 uses Windows Events for the host side of gpu->host synchronization.


## Metal
### MTLFence
Signals command buffer from command buffer.

- Created from MTLDevice
- Signaled from encoder
- Waited on by encoder

"Drivers may delay fence updates until the end of the command encoder; drivers may also wait for fences at the beginning of a command encoder. Therefore, you are not allowed to wait on a fence after it has been updated in the same command encoder."

### MTLCommandBuffer

- MTLCommandBuffer::add{Scheduled,Completed}Handler(MTLCommandBufferHandler)
- MTLCommandBuffer::waitUntil{Scheduled,Completed}()

MTLCommandBufferHandler is "A block of code to be invoked".




## Equivalency
- Host can Poll/Wait on GPU with VkFence/ID3D12Fence::SetEventOnCompletion/MTLCommandBuffer::{add*Handler,waitUntil*}().
- Command Buffer can Wait on Command Buffer with VkSemaphore/ID3D12CommandQueue::Wait/MTLFence
- Host can delay Command Buffer execution by:
  - VkEvent
  - Not submitting Command Buffers



# Proposal
Vulkan/D3D12-style queues, which are readily implementable on Metal.

D3D12 generally uses u64 signals instead of boolean signals in Vulkan and simple signal/wait in Metal.
Metal's semantics are readily implementable on the others, but are less sophisticated in comparison.

- VkFence is implementable on ID3D12Fence, and more coarsely on MTLCommandBuffer::add*/wait*.
- VkSemaphore is implementable on ID3D12CommandQueue::Signal/Wait and MTLFence.
- VkEvent is emulatable on D3D12 and Metal, but does not have a direct equivalent.
