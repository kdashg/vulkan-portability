# Pipeline State

### Glossary
- Instance (VkInstance, IDXGIFactory, -)
- Adapter (VkPhysicalDevice, IDXGIAdapter, -)
- Device (VkDevice, ID3D12Device, MTLDevice)
- Command Queue {VkQueue, ID3D12CommandQueue, MTLCommandQueue}
- Command Buffer (VkCommandBuffer, ID3D12CommandList, MTLCommandBuffer)
- Render Pass (VkRenderPass, -, MTLRenderCommandEncoder)
- Pipeline State (VkPipelineState, ID3D12PipelineState, MTLRenderPipelineState)


## Overviews

### D3D12
  - D3D12_GRAPHICS_PIPELINE_STATE_DESC
    - ID3D12RootSignature                *pRootSignature;
      - D3D12_ROOT_SIGNATURE_DESC
        - UINT                            NumParameters;
        - const D3D12_ROOT_PARAMETER      *pParameters;
          - D3D12_ROOT_PARAMETER_TYPE ParameterType;
          - union:
            - D3D12_ROOT_DESCRIPTOR_TABLE DescriptorTable;
              - UINT                         NumDescriptorRanges;
              - const D3D12_DESCRIPTOR_RANGE *pDescriptorRanges;
                - D3D12_DESCRIPTOR_RANGE_TYPE RangeType;
                - UINT                        NumDescriptors;
                - UINT                        BaseShaderRegister;
                - UINT                        RegisterSpace;
                - UINT                        OffsetInDescriptorsFromTableStart;
            - D3D12_ROOT_CONSTANTS        Constants;
              - UINT ShaderRegister;
              - UINT RegisterSpace;
              - UINT Num32BitValues;
            - D3D12_ROOT_DESCRIPTOR       Descriptor;
              - UINT ShaderRegister;
              - UINT RegisterSpace;
          - D3D12_SHADER_VISIBILITY   ShaderVisibility;
        - UINT                            NumStaticSamplers;
        - const D3D12_STATIC_SAMPLER_DESC *pStaticSamplers;
        - D3D12_ROOT_SIGNATURE_FLAGS      Flags;
    - D3D12_SHADER_BYTECODE              VS;
      - const void *pShaderBytecode;
      - SIZE_T     BytecodeLength;
    - D3D12_SHADER_BYTECODE              PS;
    - D3D12_SHADER_BYTECODE              DS;
    - D3D12_SHADER_BYTECODE              HS;
    - D3D12_SHADER_BYTECODE              GS;
    - D3D12_STREAM_OUTPUT_DESC           StreamOutput;
      - const D3D12_SO_DECLARATION_ENTRY *pSODeclaration;
        - UINT   Stream;
        - LPCSTR SemanticName;
        - UINT   SemanticIndex;
        - BYTE   StartComponent;
        - BYTE   ComponentCount;
        - BYTE   OutputSlot;
      - UINT                             NumEntries;
      - const UINT                       *pBufferStrides;
      - UINT                             NumStrides;
      - UINT                             RasterizedStream;
    - D3D12_BLEND_DESC                   BlendState;
      - BOOL                           AlphaToCoverageEnable;
      - BOOL                           IndependentBlendEnable;
      - D3D12_RENDER_TARGET_BLEND_DESC RenderTarget[8];
        - BOOL           BlendEnable;
        - BOOL           LogicOpEnable;
        - D3D12_BLEND    SrcBlend;
        - D3D12_BLEND    DestBlend;
        - D3D12_BLEND_OP BlendOp;
        - D3D12_BLEND    SrcBlendAlpha;
        - D3D12_BLEND    DestBlendAlpha;
        - D3D12_BLEND_OP BlendOpAlpha;
        - D3D12_LOGIC_OP LogicOp;
        - UINT8          RenderTargetWriteMask;
    - UINT                               SampleMask;
    - D3D12_RASTERIZER_DESC              RasterizerState;
      - D3D12_FILL_MODE                       FillMode;
      - D3D12_CULL_MODE                       CullMode;
      - BOOL                                  FrontCounterClockwise;
      - INT                                   DepthBias;
      - FLOAT                                 DepthBiasClamp;
      - FLOAT                                 SlopeScaledDepthBias;
      - BOOL                                  DepthClipEnable;
      - BOOL                                  MultisampleEnable;
      - BOOL                                  AntialiasedLineEnable;
      - UINT                                  ForcedSampleCount;
      - D3D12_CONSERVATIVE_RASTERIZATION_MODE ConservativeRaster;
    - D3D12_DEPTH_STENCIL_DESC           DepthStencilState;
      - BOOL                       DepthEnable;
      - D3D12_DEPTH_WRITE_MASK     DepthWriteMask;
      - D3D12_COMPARISON_FUNC      DepthFunc;
      - BOOL                       StencilEnable;
      - UINT8                      StencilReadMask;
      - UINT8                      StencilWriteMask;
      - D3D12_DEPTH_STENCILOP_DESC FrontFace;
        - D3D12_STENCIL_OP      StencilFailOp;
        - D3D12_STENCIL_OP      StencilDepthFailOp;
        - D3D12_STENCIL_OP      StencilPassOp;
        - D3D12_COMPARISON_FUNC StencilFunc;
      - D3D12_DEPTH_STENCILOP_DESC BackFace;
    - D3D12_INPUT_LAYOUT_DESC            InputLayout;
      - const D3D12_INPUT_ELEMENT_DESC *pInputElementDescs;
        - LPCSTR                     SemanticName;
        - UINT                       SemanticIndex;
        - DXGI_FORMAT                Format;
        - UINT                       InputSlot;
        - UINT                       AlignedByteOffset;
        - D3D12_INPUT_CLASSIFICATION InputSlotClass;
        - UINT                       InstanceDataStepRate;
      - UINT                           NumElements;
    - D3D12_INDEX_BUFFER_STRIP_CUT_VALUE IBStripCutValue;
    - D3D12_PRIMITIVE_TOPOLOGY_TYPE      PrimitiveTopologyType;
    - UINT                               NumRenderTargets;
    - DXGI_FORMAT                        RTVFormats[8];
    - DXGI_FORMAT                        DSVFormat;
    - DXGI_SAMPLE_DESC                   SampleDesc;
      - UINT Count;
      - UINT Quality;
    - UINT                               NodeMask;
    - D3D12_CACHED_PIPELINE_STATE        CachedPSO;
    - D3D12_PIPELINE_STATE_FLAGS         Flags;


### Vulkan

  - vkCreateGraphicsPipelines()
    - VkDevice                                    device,
    - VkPipelineCache                             pipelineCache,
    - uint32_t                                    createInfoCount,
    - const VkGraphicsPipelineCreateInfo*         pCreateInfos,
      - VkPipelineCreateFlags                            flags;
      - uint32_t                                         stageCount;
      - const VkPipelineShaderStageCreateInfo*           pStages;
        - VkShaderStageFlagBits               stage;
        - VkShaderModule                      module;
        - const char*                         pName;
        - const VkSpecializationInfo*         pSpecializationInfo;
          - uint32_t                           mapEntryCount;
          - const VkSpecializationMapEntry*    pMapEntries;
            - uint32_t    constantID;
            - uint32_t    offset;
            - size_t      size;
          - size_t                             dataSize;
          - const void*                        pData;
      - const VkPipelineVertexInputStateCreateInfo*      pVertexInputState;
        - uint32_t                                    vertexBindingDescriptionCount;
        - const VkVertexInputBindingDescription*      pVertexBindingDescriptions;
          - uint32_t             binding;
          - uint32_t             stride;
          - VkVertexInputRate    inputRate;
        - uint32_t                                    vertexAttributeDescriptionCount;
        - const VkVertexInputAttributeDescription*    pVertexAttributeDescriptions;
          - uint32_t    location;
          - uint32_t    binding;
          - VkFormat    format;
          - uint32_t    offset;
      - const VkPipelineInputAssemblyStateCreateInfo*    pInputAssemblyState;
        - VkPrimitiveTopology                        topology;
        - VkBool32                                   primitiveRestartEnable;
      - const VkPipelineTessellationStateCreateInfo*     pTessellationState;
        - uint32_t                                  patchControlPoints;
      - const VkPipelineViewportStateCreateInfo*         pViewportState;
        - uint32_t                              viewportCount;
        - const VkViewport*                     pViewports;
          - float    x;
          - float    y;
          - float    width;
          - float    height;
          - float    minDepth;
          - float    maxDepth;
        - uint32_t                              scissorCount;
        - const VkRect2D*                       pScissors;
          - VkOffset2D    offset;
          - VkExtent2D    extent;
      - const VkPipelineRasterizationStateCreateInfo*    pRasterizationState;
        - VkBool32                                   depthClampEnable;
        - VkBool32                                   rasterizerDiscardEnable;
        - VkPolygonMode                              polygonMode;
        - VkCullModeFlags                            cullMode;
        - VkFrontFace                                frontFace;
        - VkBool32                                   depthBiasEnable;
        - float                                      depthBiasConstantFactor;
        - float                                      depthBiasClamp;
        - float                                      depthBiasSlopeFactor;
        - float                                      lineWidth;
      - const VkPipelineMultisampleStateCreateInfo*      pMultisampleState;
        - VkSampleCountFlagBits                    rasterizationSamples;
        - VkBool32                                 sampleShadingEnable;
        - float                                    minSampleShading;
        - const VkSampleMask*                      pSampleMask;
        - VkBool32                                 alphaToCoverageEnable;
        - VkBool32                                 alphaToOneEnable;
      - const VkPipelineDepthStencilStateCreateInfo*     pDepthStencilState;
        - VkBool32                                  depthTestEnable;
        - VkBool32                                  depthWriteEnable;
        - VkCompareOp                               depthCompareOp;
        - VkBool32                                  depthBoundsTestEnable;
        - VkBool32                                  stencilTestEnable;
        - VkStencilOpState                          front;
        - VkStencilOpState                          back;
        - float                                     minDepthBounds;
        - float                                     maxDepthBounds;
      - const VkPipelineColorBlendStateCreateInfo*       pColorBlendState;
        - VkBool32                                      logicOpEnable;
        - VkLogicOp                                     logicOp;
        - uint32_t                                      attachmentCount;
        - const VkPipelineColorBlendAttachmentState*    pAttachments;
          - VkBool32                 blendEnable;
          - VkBlendFactor            srcColorBlendFactor;
          - VkBlendFactor            dstColorBlendFactor;
          - VkBlendOp                colorBlendOp;
          - VkBlendFactor            srcAlphaBlendFactor;
          - VkBlendFactor            dstAlphaBlendFactor;
          - VkBlendOp                alphaBlendOp;
          - VkColorComponentFlags    colorWriteMask;
        - float                                         blendConstants[4];
      - const VkPipelineDynamicStateCreateInfo*          pDynamicState;
        - uint32_t                             dynamicStateCount;
        - const VkDynamicState*                pDynamicStates;
          - VK_DYNAMIC_STATE_VIEWPORT = 0,
          - VK_DYNAMIC_STATE_SCISSOR = 1,
          - VK_DYNAMIC_STATE_LINE_WIDTH = 2,
          - VK_DYNAMIC_STATE_DEPTH_BIAS = 3,
          - VK_DYNAMIC_STATE_BLEND_CONSTANTS = 4,
          - VK_DYNAMIC_STATE_DEPTH_BOUNDS = 5,
          - VK_DYNAMIC_STATE_STENCIL_COMPARE_MASK = 6,
          - VK_DYNAMIC_STATE_STENCIL_WRITE_MASK = 7,
          - VK_DYNAMIC_STATE_STENCIL_REFERENCE = 8,
      - VkPipelineLayout                                 layout;
        - vkCreatePipelineLayout(VkPipelineLayoutCreateInfo)
            - uint32_t                        setLayoutCount;
            - const VkDescriptorSetLayout*    pSetLayouts;
              - vkCreateDescriptorSetLayout(VkDescriptorSetLayoutCreateInfo)
                - VkDescriptorSetLayoutCreateFlags       flags;
                - uint32_t                               bindingCount;
                - const VkDescriptorSetLayoutBinding*    pBindings;
                  - uint32_t              binding;
                  - VkDescriptorType      descriptorType;
                  - uint32_t              descriptorCount;
                  - VkShaderStageFlags    stageFlags;
                  - const VkSampler*      pImmutableSamplers;
            - uint32_t                        pushConstantRangeCount;
            - const VkPushConstantRange*      pPushConstantRanges;
              - VkShaderStageFlags    stageFlags;
              - uint32_t              offset;
              - uint32_t              size;
      - VkRenderPass                                     renderPass;
        - vkCreateRenderPass(VkRenderPassCreateInfo)
          - uint32_t                          attachmentCount;
          - const VkAttachmentDescription*    pAttachments;
            - VkAttachmentDescriptionFlags    flags;
            - VkFormat                        format;
            - VkSampleCountFlagBits           samples;
            - VkAttachmentLoadOp              loadOp;
            - VkAttachmentStoreOp             storeOp;
            - VkAttachmentLoadOp              stencilLoadOp;
            - VkAttachmentStoreOp             stencilStoreOp;
            - VkImageLayout                   initialLayout;
            - VkImageLayout                   finalLayout;
          - uint32_t                          subpassCount;
          - const VkSubpassDescription*       pSubpasses;
          - uint32_t                          dependencyCount;
          - const VkSubpassDependency*        pDependencies;
      - uint32_t                                         subpass;
      - VkPipeline                                       basePipelineHandle;
      - int32_t                                          basePipelineIndex;
    - VkPipeline*                                 pPipelines);


### Metal

  - makeRenderPipelineState(MTLRenderPipelineDescriptor)
    - vertexFunction: MTLFunction?
    - fragmentFunction: MTLFunction?
    - vertexDescriptor: MTLVertexDescriptor?
      - attributes: MTLVertexAttributeDescriptorArray
        - format: MTLVertexFormat
        - offset: Int
        - bufferIndex: Int
      - layouts: MTLVertexBufferLayoutDescriptorArray
        - stepFunction: MTLVertexStepFunction
        - stepRate: Int
        - stride: Int
    - vertexBuffers: MTLPipelineBufferDescriptorArray
      - var mutability: MTLMutability
    - fragmentBuffers: MTLPipelineBufferDescriptorArray
    - colorAttachments: MTLRenderPipelineColorAttachmentDescriptorArray
      - pixelFormat: MTLPixelFormat
      - writeMask: MTLColorWriteMask
      - isBlendingEnabled: Bool
      - alphaBlendOperation: MTLBlendOperation
      - rgbBlendOperation: MTLBlendOperation
      - destinationAlphaBlendFactor: MTLBlendFactor
      - destinationRGBBlendFactor: MTLBlendFactor
      - sourceAlphaBlendFactor: MTLBlendFactor
      - sourceRGBBlendFactor: MTLBlendFactor
    - depthAttachmentPixelFormat: MTLPixelFormat
    - stencilAttachmentPixelFormat: MTLPixelFormat
    - sampleCount: Int
    - isAlphaToCoverageEnabled: Bool
    - isAlphaToOneEnabled: Bool
    - isRasterizationEnabled: Bool
    - inputPrimitiveTopology: MTLPrimitiveTopologyClass
      - MTLPrimitiveTopologyClass.unspecified
      - MTLPrimitiveTopologyClass.point
      - MTLPrimitiveTopologyClass.line
      - MTLPrimitiveTopologyClass.triangle
    - maxTessellationFactor: Int
    - isTessellationFactorScaleEnabled: Bool
    - tessellationFactorFormat: MTLTessellationFactorFormat
      - MTLTessellationFactorFormat.half
    - tessellationControlPointIndexType: MTLTessellationControlPointIndexType
      - MTLTessellationControlPointIndexType.none
      - MTLTessellationControlPointIndexType.uint16
      - MTLTessellationControlPointIndexType.uint32
    - tessellationFactorStepFunction: MTLTessellationFactorStepFunction
      - MTLTessellationFactorStepFunction.constant
      - MTLTessellationFactorStepFunction.perPatch
      - MTLTessellationFactorStepFunction.perInstance
      - MTLTessellationFactorStepFunction.perPatchAndPerInstance
    - tessellationOutputWindingOrder: MTLWinding
    - tessellationPartitionMode: MTLTessellationPartitionMode
      - MTLTessellationPartitionMode.pow2
      - MTLTessellationPartitionMode.integer
      - MTLTessellationPartitionMode.fractionalOdd
      - MTLTessellationPartitionMode.fractionalEven
    - rasterSampleCount: Int



## By concepts and structs

### Viewports

~~~
typedef struct VkPipelineViewportStateCreateInfo {
    VkStructureType                       sType;
    const void*                           pNext;
    VkPipelineViewportStateCreateFlags    flags;
    uint32_t                              viewportCount;
    const VkViewport*                     pViewports;
    uint32_t                              scissorCount;
    const VkRect2D*                       pScissors;
} VkPipelineViewportStateCreateInfo;

typedef enum VkDynamicState {
    VK_DYNAMIC_STATE_VIEWPORT = 0,
    VK_DYNAMIC_STATE_SCISSOR = 1,
    VK_DYNAMIC_STATE_LINE_WIDTH = 2,
    VK_DYNAMIC_STATE_DEPTH_BIAS = 3,
    VK_DYNAMIC_STATE_BLEND_CONSTANTS = 4,
    VK_DYNAMIC_STATE_DEPTH_BOUNDS = 5,
    VK_DYNAMIC_STATE_STENCIL_COMPARE_MASK = 6,
    VK_DYNAMIC_STATE_STENCIL_WRITE_MASK = 7,
    VK_DYNAMIC_STATE_STENCIL_REFERENCE = 8,
} VkDynamicState;
~~~

D3D always uses in-command-buffer dynamic viewports with ID3D12CommandList.RSSetViewports.
In Vulkan, this is available by using VK_DYNAMIC_STATE_VIEWPORT with VkPipelineDynamicStateCreateInfo::pDynamicStates.

### Rasterizer state

~~~
typedef struct D3D12_RASTERIZER_DESC {
  D3D12_FILL_MODE                       FillMode;
  D3D12_CULL_MODE                       CullMode;
  BOOL                                  FrontCounterClockwise;
  INT                                   DepthBias;
  FLOAT                                 DepthBiasClamp;
  FLOAT                                 SlopeScaledDepthBias;
  BOOL                                  DepthClipEnable;
  BOOL                                  MultisampleEnable;
  BOOL                                  AntialiasedLineEnable;
  UINT                                  ForcedSampleCount;
  D3D12_CONSERVATIVE_RASTERIZATION_MODE ConservativeRaster;
} D3D12_RASTERIZER_DESC;

typedef struct VkPipelineRasterizationStateCreateInfo {
    VkStructureType                            sType;
    const void*                                pNext;
    VkPipelineRasterizationStateCreateFlags    flags;
    VkBool32                                   depthClampEnable;
    VkBool32                                   rasterizerDiscardEnable;
    VkPolygonMode                              polygonMode;
    VkCullModeFlags                            cullMode;
    VkFrontFace                                frontFace;
    VkBool32                                   depthBiasEnable;
    float                                      depthBiasConstantFactor;
    float                                      depthBiasClamp;
    float                                      depthBiasSlopeFactor;
    float                                      lineWidth;
} VkPipelineRasterizationStateCreateInfo;
~~~

- FillMode :: polygonMode (d3d lacks a points option)
- CullMode :: cullMode
- FrontCounterClockwise :: frontFace
- DepthBias :: depthBiasEnable,depthBiasConstantFactor
- DepthBiasClamp :: depthBiasClamp
- SlopeScaledDepthBias :: depthBiasSlopeFactor
- DepthClipEnable :: depthClampEnable

Line antialiasing:
no line aa:
  - MultisampleEnable:false, AntialiasedLineEnable:false
  - rasterizationSamples:VK_SAMPLE_COUNT_1_BIT
alpha line anti-aliasing:
  - MultisampleEnable:false, AntialiasedLineEnable:true
  - VkPhysicalDeviceLimits.strictLines:true
quadrilateral/parallelogram:
  - MultisampleEnable:true
  - VkPhysicalDeviceLimits.strictLines:false

- ForcedSampleCount :: -
- ConservativeRaster :: -

In D3D11, the equivalent to Vulkan's `rasterizerDiscardEnable` is specifying null for the pixel shader, and disabling depth and stencil testing. The same is likely true of D3D12.

### Blend state

~~~
typedef struct D3D12_BLEND_DESC {
  BOOL                           AlphaToCoverageEnable;
  BOOL                           IndependentBlendEnable;
  D3D12_RENDER_TARGET_BLEND_DESC RenderTarget[8];
} D3D12_BLEND_DESC;

typedef struct D3D12_RENDER_TARGET_BLEND_DESC {
  BOOL           BlendEnable;
  BOOL           LogicOpEnable;
  D3D12_BLEND    SrcBlend;
  D3D12_BLEND    DestBlend;
  D3D12_BLEND_OP BlendOp;
  D3D12_BLEND    SrcBlendAlpha;
  D3D12_BLEND    DestBlendAlpha;
  D3D12_BLEND_OP BlendOpAlpha;
  D3D12_LOGIC_OP LogicOp;
  UINT8          RenderTargetWriteMask;
} D3D12_RENDER_TARGET_BLEND_DESC;


typedef struct VkPipelineColorBlendStateCreateInfo {
    VkStructureType                               sType;
    const void*                                   pNext;
    VkPipelineColorBlendStateCreateFlags          flags;
    VkBool32                                      logicOpEnable;
    VkLogicOp                                     logicOp;
    uint32_t                                      attachmentCount;
    const VkPipelineColorBlendAttachmentState*    pAttachments;
    float                                         blendConstants[4];
} VkPipelineColorBlendStateCreateInfo;

typedef struct VkPipelineColorBlendAttachmentState {
    VkBool32                 blendEnable;
    VkBlendFactor            srcColorBlendFactor;
    VkBlendFactor            dstColorBlendFactor;
    VkBlendOp                colorBlendOp;
    VkBlendFactor            srcAlphaBlendFactor;
    VkBlendFactor            dstAlphaBlendFactor;
    VkBlendOp                alphaBlendOp;
    VkColorComponentFlags    colorWriteMask;
} VkPipelineColorBlendAttachmentState;
~~~

### Depth-stencil state

~~~
typedef struct D3D12_DEPTH_STENCIL_DESC {
  BOOL                       DepthEnable;
  D3D12_DEPTH_WRITE_MASK     DepthWriteMask;
  D3D12_COMPARISON_FUNC      DepthFunc;
  BOOL                       StencilEnable;
  UINT8                      StencilReadMask;
  UINT8                      StencilWriteMask;
  D3D12_DEPTH_STENCILOP_DESC FrontFace;
  D3D12_DEPTH_STENCILOP_DESC BackFace;
} D3D12_DEPTH_STENCIL_DESC;

typedef struct D3D12_DEPTH_STENCILOP_DESC {
  D3D12_STENCIL_OP      StencilFailOp;
  D3D12_STENCIL_OP      StencilDepthFailOp;
  D3D12_STENCIL_OP      StencilPassOp;
  D3D12_COMPARISON_FUNC StencilFunc;
} D3D12_DEPTH_STENCILOP_DESC;

typedef struct VkPipelineDepthStencilStateCreateInfo {
    VkStructureType                           sType;
    const void*                               pNext;
    VkPipelineDepthStencilStateCreateFlags    flags;
    VkBool32                                  depthTestEnable;
    VkBool32                                  depthWriteEnable;
    VkCompareOp                               depthCompareOp;
    VkBool32                                  depthBoundsTestEnable;
    VkBool32                                  stencilTestEnable;
    VkStencilOpState                          front;
    VkStencilOpState                          back;
    float                                     minDepthBounds;
    float                                     maxDepthBounds;
} VkPipelineDepthStencilStateCreateInfo;

typedef struct VkStencilOpState {
    VkStencilOp    failOp;
    VkStencilOp    passOp;
    VkStencilOp    depthFailOp;
    VkCompareOp    compareOp;
    uint32_t       compareMask;
    uint32_t       writeMask;
    uint32_t       reference;
} VkStencilOpState;

MTLDepthStencilDescriptor
  - depthCompareFunction: MTLCompareFunction
  - isDepthWriteEnabled: Bool
  - backFaceStencil: MTLStencilDescriptor!
  - frontFaceStencil: MTLStencilDescriptor!

MTLStencilDescriptor
  - stencilFailureOperation: MTLStencilOperation
  - depthFailureOperation: MTLStencilOperation
  - depthStencilPassOperation: MTLStencilOperation
  - stencilCompareFunction: MTLCompareFunction
  - readMask: UInt32
  - writeMask: UInt32

 - :: depthBoundsTestEnable, minDepthBounds, maxDepthBounds
~~~

Notes:
  - Metal depth stencil state is not stored in the pipeline state but instead set on a `MTLRenderCommandEncoder`. It is included here for sake of comparison.
  - Metal implicitly enables and disables the depth and stencil tests. if `depthCompareFunction == MTLCompareFunctionAlways`, the depth test is off. If `backFaceStencil == nil` or `frontFaceStencil = nil`, the back or front face stencil tests, respectively, are disabled.
  - D3D12 does not have separate read / write masks. Vulkan and Metal have per-face read / write masks.
  - D3D12 does not have separate stencil reference values. Vulkan and Metal have per-face stencil references.
  - Vulkan can do both static and dynamic stencil references. D3D12 and Metal on have dynamic stencil references.

### Multisampling state

~~~
typedef struct VkPipelineMultisampleStateCreateInfo {
    VkStructureType                          sType;
    const void*                              pNext;
    VkPipelineMultisampleStateCreateFlags    flags;
    VkSampleCountFlagBits                    rasterizationSamples;
    VkBool32                                 sampleShadingEnable;
    float                                    minSampleShading;
    const VkSampleMask*                      pSampleMask;
    VkBool32                                 alphaToCoverageEnable;
    VkBool32                                 alphaToOneEnable;
} VkPipelineMultisampleStateCreateInfo;


D3D12_GRAPHICS_PIPELINE_STATE_DESC:
  UINT                               SampleMask;
  DXGI_SAMPLE_DESC                   SampleDesc;

D3D12_BLEND_DESC:
  BOOL                           AlphaToCoverageEnable;

typedef struct DXGI_SAMPLE_DESC {
  UINT Count;
  UINT Quality;
} DXGI_SAMPLE_DESC;
~~~

- D3D12_GRAPHICS_PIPELINE_STATE_DESC.SampleDesc.Count :: rasterizationSamples
- SampleMask :: pSampleMask[0] (pSampleMask supports >32 bits of masking)

### Render target formats

~~~
  UINT                               NumRenderTargets;
  DXGI_FORMAT                        RTVFormats[8];
  DXGI_FORMAT                        DSVFormat;
~~~

~~~
    VkRenderPass renderPass;
~~~
renderPass is a handle to a render pass object describing the environment in which the pipeline will be used; the pipeline must only be used with an instance of any render pass compatible with the one provided.

Render Pass Compatibility:
Two attachment references are compatible if they have matching format and sample count, or are both VK_ATTACHMENT_UNUSED or the pointer that would contain the reference is NULL.

Two arrays of attachment references are compatible if all corresponding pairs of attachments are compatible. If the arrays are of different lengths, attachment references not present in the smaller array are treated as VK_ATTACHMENT_UNUSED.

Two render passes are compatible if their corresponding color, input, resolve, and depth/stencil attachment references are compatible and if they are otherwise identical except for:

- Initial and final image layout in attachment descriptions
- Load and store operations in attachment descriptions
- Image layout in attachment references

~~~
MTLRenderPipelineDescriptor:
  - : MTLRenderPipelineColorAttachmentDescriptor[]
    - pixelFormat: MTLPixelFormat
  - depthAttachmentPixelFormat: MTLPixelFormat
  - stencilAttachmentPixelFormat: MTLPixelFormat
~~~

Functionally, the VkRenderPass arg for pipeline state creation provides the NumRenderTargets/RTVFormats/DSVFormat vars for the pipeline.

