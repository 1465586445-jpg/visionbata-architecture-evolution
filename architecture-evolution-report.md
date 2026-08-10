# VisionBata 项目目录树

> **生成日期**：2026-08-10
> **范围**：`F:\VisionBata` 全量（不含 venv/、`__pycache__/`）
> **总 Python 源文件**：144 个，约 50,754 行
> **对比 07-18**：+85 文件，+30,334 行（+148%）。新增 `benchmark/`、`config/`；visioncore/ 从 20→73 (+53文件)；tests/ 从 8→38 (+30文件)；docs/ 从 12→57 (+45文件)；ai/ +1 (`inference_controller.py`)

---

```
VisionBata/
│
├── main.py                                120行  应用入口：异常钩子 + 高DPI + QApplication 引导
├── start.bat                                      Windows 启动脚本（硬编码 venv 路径）
├── requirements.txt                               第三方依赖清单
├── error.log                                      ~17MB 运行错误日志
├── PROJECT_BIBLE.md                               项目权威技术白皮书（v1.1.1, 2026-07-10）
├── demo.py                                552行  演示脚本（独立于主应用）
├── debug-yolo-detection-overlay.md                 YOLO 检测叠加层调试记录
│
├── models/                                        模型文件（10个，运行时依赖）
│   ├── yolov8n.pt / yolov8n.onnx
│   ├── yolov8n-pose.pt / yolov8n-pose.onnx
│   ├── yolo26n.pt / yolo26n-obb.pt / yolo26s.pt
│   ├── uhd_n_64x64_static.onnx                    PINTO0309 UHD 人体检测
│   ├── chc_s_wo_fiqa.onnx                         PINTO0309 CHC 头部属性
│   └── test_obb.jpg
│
├── ai/                                            ★ 推理与识别核心（14文件, 6,082行）
│   ├── __init__.py                         1行    仅导出 InferWorker
│   ├── inference.py                    2,169行    ★ InferWorker 共享推理线程（+EventBus Shadow）
│   ├── inference_controller.py           404行    ★ 推理控制器（★ 新增）
│   ├── tracker.py                      1,423行    ★ DetectionTracker + UKF/MCUKF/Manifold + AUTO
│   ├── detection.py                      196行    Detection/Keypoint 统一数据模型
│   ├── model_task.py                      12行    ModelTask 枚举 (DETECT/POSE/OBB)
│   ├── onnx_yolo_backend.py              322行    ONNX Runtime/DirectML YOLO 后端
│   ├── yolo26_backend.py                 158行    YOLO26 .pt 后端（ultralytics）
│   ├── uhd_backend.py                    132行    PINTO0309 UHD 64x64 人体检测
│   ├── head_classifier.py                171行    CHC 头部属性分类
│   ├── reid_backend.py                   136行    ReID 重识别嵌入后端（mock 256维）
│   ├── rectangle_detector.py             549行    矩形检测 + 线性卡尔曼跟踪
│   ├── gesture_recognizer.py             180行    身体姿态手势（17 COCO 关键点几何）
│   └── hand_gesture_recognizer.py        229行    手部手势（MediaPipe Hands，已禁用）
│
├── gui/                                            PyQt6 界面层（8文件, 5,276行）
│   ├── __init__.py                         0行    空包初始化
│   ├── main_window.py                  1,319行    ★ MainWindow 主窗口 + 信号槽调度中心
│   ├── camera_cell.py                  1,358行    ★ CameraCell 单路画面 + 叠加绘制
│   ├── settings_dialog.py              1,148行    SettingsDialog 配置编辑 + QSettings 持久化
│   ├── theme.py                          799行    冷蓝灰暗色 Fusion QSS 主题
│   ├── animations.py                     465行    Qt 动画系统（12种快捷动画）
│   ├── splash_screen.py                  152行    SplashScreen 启动闪屏
│   └── safe_widgets.py                    35行    防滚轮抢焦点控件
│
├── camera/                                         采集与录像层（4文件, 591行）
│   ├── __init__.py                         3行    导出相机枚举/采集/录像组件
│   ├── enumerator.py                     289行    CameraInfo/scan_cameras 三级枚举
│   ├── capture.py                        137行    SingleCameraWorker(PyAV dshow) + CameraCapture
│   └── recorder.py                       162行    VideoRecorder(H.264 MP4) + 守护线程池
│
├── common/                                         公共抽象层（3文件, 320行）
│   ├── __init__.py                       109行    Protocol 接口 + Detection/DetectionTracker 重导出
│   ├── pyav_capture.py                   107行    PyAV 采集后端实现
│   └── yolo_detector.py                  104行    YOLO 检测后端实现
│
├── visioncore/                                     ★ 新架构重构层（73文件, 17,815行）
│   ├── __init__.py                        41行    包入口，版本标记
│   │
│   ├── core/                                      核心数据模型（7文件, 816行）
│   │   ├── adapters.py                   308行    ai↔visioncore 桥接（TYPE_CHECKING）
│   │   ├── target.py                     139行    Target 数据类（frozen slots）
│   │   ├── detection.py                   82行    CoreDetection 数据类
│   │   ├── __init__.py                    77行    统一导出
│   │   ├── frame.py                       72行    Frame 数据类
│   │   ├── track.py                       72行    Track 数据类
│   │   └── event.py                       66行    Event 数据类
│   │
│   ├── state/                                     TargetState 快照（2文件, 429行）
│   │   ├── target_state.py               389行    TargetState 快照 dataclass（frozen+slots, 14字段）
│   │   └── __init__.py                    40行    导出
│   │
│   ├── eventbus/                                  ★ 发布-订阅基础设施 — M3（6文件, 1,286行）
│   │   ├── bus.py                        480行    ★ EventBus 核心（RLock + 锁外分发 + 双模式订阅）
│   │   ├── events.py                     241行    类型化事件层级（BaseEvent + 5生命周期事件）
│   │   ├── debug_logger.py               217行    DebugEventLogger 可切换通配符调试观察者
│   │   ├── subscriber.py                 175行    Subscriber dataclass + EventListener 别名
│   │   ├── dispatcher.py                  90行    Dispatcher 同步分发引擎（异常隔离）
│   │   └── __init__.py                    83行    包导出
│   │
│   ├── target_manager/                            目标管理业务逻辑（6文件, 1,766行）
│   │   ├── manager.py                    718行    ★ TargetManager + EventBus 集成
│   │   ├── converters.py                 348行    Detection↔Track 转换器
│   │   ├── lifecycle.py                  319行    TargetLifecycleManager 纯状态机
│   │   ├── store.py                      208行    TargetStore 多槽位存储
│   │   ├── __init__.py                    91行    统一导出
│   │   └── target_id_factory.py           82行    TargetIdFactory 统一ID生成
│   │
│   ├── protocol/                                  ★ 协议/序列化/发布层（11文件, 2,466行）★ 新增
│   │   ├── base.py                       240行    ProtocolAdapter ABC
│   │   ├── console_adapter.py            151行    Console 日志适配器
│   │   ├── null_adapter.py               112行    Null 空操作适配器
│   │   ├── __init__.py                    54行    导出
│   │   ├── serialization/
│   │   │   ├── cbor_serializer.py        270行    CBOR 二进制序列化
│   │   │   ├── json_serializer.py        245行    JSON 文本序列化
│   │   │   └── __init__.py                45行    导出
│   │   ├── adapters/
│   │   │   ├── udp_adapter.py            311行    UDP 网络传输适配器
│   │   │   └── __init__.py                47行    导出
│   │   └── publisher/
│   │       ├── shadow_publisher.py       474行    Shadow 旁路发布器
│   │       ├── state_publisher.py        404行    状态发布器
│   │       └── __init__.py                48行    导出
│   │
│   ├── pipeline/                                  ★ 新一代 Pipeline 架构（20文件, 6,973行）★ 新增
│   │   ├── shadow.py                     553行    Shadow 旁路执行器
│   │   ├── pipeline.py                   538行    Pipeline 顺序执行器（三态状态机）
│   │   ├── adapters.py                   388行    适配器层
│   │   ├── migration.py                  277行    迁移辅助
│   │   ├── base.py                       274行    PipelineStage ABC（4方法生命周期）
│   │   ├── stage.py                      247行    DummyStage 测试用占位 stage
│   │   ├── context.py                    209行    PipelineContext 数据黑板（7字段）
│   │   ├── __init__.py                    91行    导出
│   │   ├── config/
│   │   │   ├── pipeline_config.py        420行    Pipeline 配置管理
│   │   │   └── __init__.py                24行    导出
│   │   └── stages/                                    具体 Stage 实现
│   │       ├── target_stage.py           634行    ★ 目标管理 Stage
│   │       ├── tracker_stage.py          521行    ★ 跟踪 Stage
│   │       ├── detector_stage.py         482行    ★ 检测 Stage
│   │       ├── event_stage.py            440行    事件发布 Stage
│   │       ├── rectangle_filter_stage.py 442行    矩形过滤 Stage
│   │       ├── redetect_stage.py         316行    ROI 重检测 Stage
│   │       ├── attribute_gesture_stage.py 297行    属性+手势 Stage
│   │       ├── denoise_stage.py          219行    去噪 Stage
│   │       ├── __init__.py                98行    导出
│   │       ├── advanced/
│   │       │   ├── advanced_stage.py     618行    高级 Stage（插件驱动）
│   │       │   ├── stage_capability.py   136行    Stage 能力标记
│   │       │   └── __init__.py            57行    导出
│   │       └── redetect/
│   │           ├── redetect.py           480行    ROI 重检测核心逻辑
│   │           └── __init__.py            31行    导出
│   │
│   ├── plugin/                                     ★ 插件系统（5文件, 1,416行）★ 新增
│   │   ├── base.py                       596行    PluginBase ABC + 生命周期
│   │   ├── registry.py                   331行    PluginRegistry 注册表
│   │   ├── __init__.py                    67行    导出
│   │   └── processors/
│   │       ├── processor.py              394行    处理器基础类
│   │       └── __init__.py                28行    导出
│   │
│   ├── source/                                     ★ 输入源统一抽象（4文件, 924行）★ 新增
│   │   ├── base.py                       321行    FrameSource ABC（5方法生命周期）
│   │   ├── dummy_source.py               317行    DummyFrameSource（固定输出/重放/循环）
│   │   ├── frame_source.py               193行    工厂 + 注册表
│   │   └── __init__.py                    93行    导出
│   │
│   ├── runtime/                                    ★ 运行时管理（5文件, 902行）★ 新增
│   │   ├── __init__.py                    37行    导出
│   │   ├── model/
│   │   │   ├── model_runtime.py          425行    模型运行时管理
│   │   │   └── __init__.py                25行    导出
│   │   └── state/
│   │       ├── runtime_state.py          390行    运行时状态追踪
│   │       └── __init__.py                25行    导出
│   │
│   └── config/                                     配置文件（1文件, 42行）★ 新增
│       └── __init__.py                    42行    配置入口
│
├── tests/                                          ★ 测试（38文件, 19,440行, ~830+项）★ 大幅增长
│   ├── test_event_bus.py               1,112行    77项 EventBus 全量测试
│   ├── test_target_manager.py            947行    76项 TargetManager 测试
│   ├── test_pipeline.py                  893行    56项 Pipeline 框架测试
│   ├── test_frame_source.py              735行    55项 FrameSource 测试
│   ├── test_rectangle_filter_stage.py    723行    矩形过滤 Stage 测试
│   ├── test_target_state.py              707行    TargetState 快照测试
│   ├── test_tracker_stage.py             703行    跟踪 Stage 测试
│   ├── test_state_publisher.py           697行    状态发布器测试
│   ├── test_shadow_publisher.py          691行    Shadow 发布器测试
│   ├── test_advanced_stage.py            669行    高级 Stage 测试
│   ├── test_target_stage.py              666行    目标管理 Stage 测试
│   ├── test_detector_stage.py            636行    检测 Stage 测试
│   ├── test_udp_adapter.py               606行    UDP 适配器测试
│   ├── test_protocol_base.py             589行    协议基础测试
│   ├── test_redetect_migration.py        561行    重检测迁移测试
│   ├── test_json_serializer.py           545行    JSON 序列化测试
│   ├── test_event_stage.py               541行    事件 Stage 测试
│   ├── test_attribute_gesture_stage.py   522行    属性+手势 Stage 测试
│   ├── test_cbor_serializer.py           521行    CBOR 序列化测试
│   ├── test_shadow_pipeline.py           498行    Shadow Pipeline 测试
│   ├── test_target_manager_events.py     492行    29项 生命周期事件测试
│   ├── test_model_runtime.py             478行    模型运行时测试
│   ├── test_inferworker_migration.py     470行    InferWorker 迁移测试
│   ├── test_plugin_base.py               454行    插件基础测试
│   ├── test_runtime_state.py             454行    运行时状态测试
│   ├── test_redetect_stage.py            428行    重检测 Stage 测试
│   ├── test_processor_migration.py       406行    处理器迁移测试
│   ├── test_core_models.py               396行    31项 核心数据模型测试
│   ├── test_denoise_stage.py             395行    去噪 Stage 测试
│   ├── test_pipeline_config.py           390行    Pipeline 配置测试
│   ├── test_debug_logger.py              367行    20项 DebugEventLogger 测试
│   ├── test_plugin_registry.py           352行    插件注册表测试
│   ├── test_shadow_integration.py        282行    12项 Shadow 集成测试
│   ├── test_inferworker_controller.py    257行    InferWorker 控制器测试
│   ├── test_matching.py                  141行    8项 跟踪匹配策略测试
│   ├── test_obb_stage2.py                112行    7项 OBB polygon IoU 测试
│   ├── __init__.py                         0行    包初始化
│   └── _plugin_fixtures.py                 4行    插件测试夹具
│
├── benchmark/                                      性能基准（1文件, 258行）★ 新增
│   └── benchmark_serializer.py           258行    序列化器性能基准测试
│
├── tools/                                          工具（1文件, 300行）
│   └── mot_eval.py                       300行    离线 MOT 评估工具
│
├── config/                                         配置文件 ★ 新增
│   └── visioncore_pipeline.json                   Pipeline JSON 配置
│
├── docs/                                           设计文档（57份）★ 大幅增长
│   ├── auto-filter-adaptive-plan.md                AUTO 自适应滤波方案
│   ├── denoise-bilateral-nlmeans-plan.md            去噪方案
│   ├── eventbus-audit-report.md                     EventBus 阶段验收审计
│   ├── mcukf-manifold-ukf-plan.md                   MCUKF/Manifold UKF 方案
│   ├── pinto0309-uhd-chc-integration-plan.md        PINTO0309 集成方案
│   ├── sentineltrack-2.0-architecture-blueprint.md  SentinelTrack 2.0 蓝图
│   ├── strategic-roadmap.md                         战略路线图
│   ├── milestone2-audit-report.md / cleanup          Milestone 2 审计/清理
│   ├── performance-degradation-analysis.md          性能下降分析
│   ├── project-audit-report.md                      项目审计
│   ├── multislot-isolation-report.md                多槽位隔离修复
│   │
│   ├── protocol-foundation.md                      协议基础设计 ★
│   ├── targetstate-design.md                        TargetState 设计 ★
│   ├── targetstate-audit-report.md                  TargetState 审计 ★
│   ├── targetstate-hotfix.md                        TargetState 热修复 ★
│   ├── udp-adapter.md                               UDP 适配器设计 ★
│   ├── json-serializer.md                           JSON 序列化器 ★
│   ├── cbor-benchmark.md                            CBOR 性能基准 ★
│   ├── state-publisher.md                           状态发布器 ★
│   │
│   ├── frame-source.md                              FrameSource 设计 ★
│   ├── frame-source-audit.md                        FrameSource 审计 ★
│   ├── pipeline-foundation.md                       Pipeline 基础设计 ★
│   ├── pipeline-audit-report.md                     Pipeline 审计 ★
│   ├── pipeline-config.md                           Pipeline 配置 ★
│   ├── pipeline-config-audit.md                     Pipeline 配置审计 ★
│   ├── pipeline-shadow-report.md                    Pipeline Shadow 报告 ★
│   │
│   ├── detector-stage.md                            检测 Stage 设计 ★
│   ├── tracker-stage.md                             跟踪 Stage 设计 ★
│   ├── target-stage.md                              目标管理 Stage ★
│   ├── event-stage.md                               事件 Stage 设计 ★
│   ├── denoise-stage.md                             去噪 Stage ★
│   ├── denoise-stage-audit.md                       去噪 Stage 审计 ★
│   ├── redetect-stage.md                            重检测 Stage ★
│   ├── redetect-stage-audit.md                      重检测 Stage 审计 ★
│   ├── redetect-migration-report.md                 重检测迁移报告 ★
│   ├── redetect-migration-audit.md                  重检测迁移审计 ★
│   ├── rectangle-filter-stage.md                    矩形过滤 Stage ★
│   ├── rectangle-filter-stage-audit.md              矩形过滤 Stage 审计 ★
│   ├── attribute-gesture-stage.md                   属性+手势 Stage ★
│   ├── attribute-gesture-stage-audit.md             属性+手势 Stage 审计 ★
│   ├── advanced-stage-foundation.md                 高级 Stage 基础 ★
│   ├── advanced-stage-audit.md                      高级 Stage 审计 ★
│   │
│   ├── plugin-interface.md                          插件接口设计 ★
│   ├── plugin-interface-audit.md                    插件接口审计 ★
│   ├── plugin-registry.md                           插件注册表 ★
│   ├── plugin-registry-audit.md                     插件注册表审计 ★
│   ├── processor-migration.md                       处理器迁移 ★
│   ├── model-runtime.md                             模型运行时 ★
│   ├── runtime-state.md                             运行时状态 ★
│   │
│   ├── inferworker-migration.md                     InferWorker 迁移 ★
│   ├── inferworker-migration-audit.md               InferWorker 迁移审计 ★
│   ├── inferworker-final-audit.md                   InferWorker 最终审计 ★
│   ├── shadow-integration-report.md                 Shadow 集成报告 ★
│   ├── stage-c-final-report.md                      Stage C 最终报告 ★
│   ├── stage-d-final-report.md                      Stage D 最终报告 ★
│   └── before_after_report.md                       前后对比报告 ★
│
├── architecture/                                   架构文档归档（10份）
│   ├── VisionBata项目全量档案-2026-07-18.md          07-18 全量档案
│   ├── 架构审计报告-2026-07-18.md                     v2.0 审计
│   ├── 架构审计报告-2026-07-16.md                     v1.0 审计
│   ├── 架构总结-2026-07-14.md                         第三版总结
│   ├── 架构梳理报告-2026-07-08.md / 07-09.md          初版/第二版
│   ├── 项目目录树-2026-07-18.md                       07-18 目录树
│   ├── eventbus-audit-report.md                      EventBus 审计
│   └── PROJECT_BIBLE.md                              白皮书副本
│
└── recordings/                                     录像输出
    ├── slot_0_20260609_205202.mp4
    └── slot_0_20260707_022612.mp4
```

---

## 按目录规模汇总

| 目录 | 文件数 | 行数 | 占比 | 对比 07-18 |
|------|--------|------|------|------------|
| `tests/` | 38 | 19,440 | 38.3% | +15,591 |
| `visioncore/` | 73 | 17,815 | 35.1% | +13,906 |
| `ai/` | 14 | 6,082 | 12.0% | +555 |
| `gui/` | 8 | 5,276 | 10.4% | +24 |
| `camera/` | 4 | 591 | 1.2% | 0 |
| `demo.py` | 1 | 552 | 1.1% | 0 |
| `common/` | 3 | 320 | 0.6% | 0 |
| `tools/` | 1 | 300 | 0.6% | 0 |
| `benchmark/` | 1 | 258 | 0.5% | **NEW** |
| `main.py` | 1 | 120 | 0.2% | 0 |
| **总计** | **144** | **50,754** | **100%** | **+30,334** |

## visioncore/ 子包明细

| 子包 | 文件数 | 行数 | 说明 |
|------|--------|------|------|
| `pipeline/` | 20 | 6,973 | ★ 新一代 Pipeline 架构（最大子包） |
| `protocol/` | 11 | 2,466 | 协议/序列化/发布层 |
| `target_manager/` | 6 | 1,766 | 目标管理业务逻辑 |
| `plugin/` | 5 | 1,416 | 插件系统 |
| `eventbus/` | 6 | 1,286 | 发布-订阅基础设施 |
| `source/` | 4 | 924 | 输入源统一抽象 |
| `runtime/` | 5 | 902 | 运行时管理 |
| `core/` | 7 | 816 | 核心数据模型 |
| `state/` | 2 | 429 | TargetState 快照 |

## 最大文件 Top 15

| 排名 | 文件 | 行数 | 类别 |
|------|------|------|------|
| 1 | `ai/inference.py` | 2,169 | ★ 核心推理编排器 |
| 2 | `ai/tracker.py` | 1,423 | ★ 多目标跟踪 + UKF 族 |
| 3 | `gui/camera_cell.py` | 1,358 | 画面显示 + 叠加绘制 |
| 4 | `gui/main_window.py` | 1,319 | 主窗口 + 信号槽调度 |
| 5 | `gui/settings_dialog.py` | 1,148 | 配置管理 |
| 6 | `tests/test_event_bus.py` | 1,112 | EventBus 77项测试 |
| 7 | `tests/test_target_manager.py` | 947 | TargetManager 76项测试 |
| 8 | `tests/test_pipeline.py` | 893 | Pipeline 56项测试 |
| 9 | `gui/theme.py` | 799 | 暗色 QSS 主题 |
| 10 | `tests/test_frame_source.py` | 735 | FrameSource 55项测试 |
| 11 | `tests/test_rectangle_filter_stage.py` | 723 | 矩形过滤 Stage 测试 |
| 12 | `visioncore/target_manager/manager.py` | 718 | TargetManager + EventBus |
| 13 | `tests/test_target_state.py` | 707 | TargetState 测试 |
| 14 | `tests/test_tracker_stage.py` | 703 | 跟踪 Stage 测试 |
| 15 | `tests/test_state_publisher.py` | 697 | 状态发布器测试 |

---

## 对比 07-18 关键变化

| 维度 | 07-18 | 08-10 | 变化 |
|------|-------|-------|------|
| Python 源文件 | 59 | **144** | +85 (+144%) |
| 总代码行 | 20,420 | **50,754** | +30,334 (+149%) |
| visioncore/ 文件 | 20 | **73** | +53 (+265%) |
| tests/ 文件 | 8 | **38** | +30 (+375%) |
| docs/ 文件 | 12 | **57** | +45 (+375%) |
| 新顶层目录 | — | benchmark/, config/ | +2 |
| visioncore 新子包 | — | pipeline/, protocol/, plugin/, source/, runtime/, state/, config/ | +7 |
| ai/ 新文件 | — | inference_controller.py | +1 |
