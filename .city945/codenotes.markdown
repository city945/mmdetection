代码笔记文件语法规范: **强调** 语法用来标识重点关注的代码，~~删除线~~ 语法用来标识可忽略的代码，<datastruct> HTML 语法表示引用的数据结构，[module] 链接语法用来标识链接到模块代码

#### 代码框架图

#### 模块实现层
<!-- 模型和数据预处理器 -->

- ResNet
  - \__init__
    """
    创建模型参数初始化配置，如没有指定预训练模型路径则采用默认配置即 Kaiming 初始化卷积层等
    
    Configs:
      depth: 18/50/101 等层数
      num_stages: 阶段数，一般为默认值 4 即可
      style: 
      base_channels: 默认 64，基础通道数，通道升维倍数基于此值
      deep_stem: 是否采用多层主干层
      stem_channels: 主干层输出通道
      with_cp: 是否使用梯度检查点技术，以时间换空间，不保存中间梯度而是在反向传播时重新计算
      dcn: 可变形卷积，默认 None，可选参数如 dict(type='DCN',...)、'DCNv2'等
      stage_with_dcn: tuple of bool，某个阶段是否采用可变形卷积
      dilations:
      plugins: list of dict，某个阶段的自定义层，默认 None，可选参数如 dict(cfg, position='after_conv1', stages=(False,False,False,True))
    """
    - _make_stem_layer: 如果 deep_stem 则主干层包括多层 Conv-BN-ReLU，否则一层
    - make_stage_plugins: 获取当前阶段的自定义层参数
    - make_res_layer/[ResLayer]+[BasicBlock]
    - _freeze_stages: 设置 requires_grad 为 False
  - forward
- ResLayer.\__init__: 

- DetDataPreprocessor/ImgDataPreprocessor: 目标检测/分割任务数据预处理器，额外支持批量数据增强以及同步操作真值
  """
  执行父类函数，递归搬运数据到目标设备，依次执行数据变换 (1) 交换颜色通道 (2) 图像归一化 (3) 边界填充
  重新计算边界填充后每个图像的尺寸，如果输入为图像列表则为尺寸列表，如果输入为图像张量则为单个尺寸，并后续添加到数据样本实例的元信息中
  同步操作数据样本实例中的真值
  执行批量数据增强如 Mosaic
  """
