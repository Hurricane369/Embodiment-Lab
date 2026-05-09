<script setup>
import { ref, computed, onMounted, onBeforeUnmount, nextTick } from 'vue'
import gsap from 'gsap'
import { ScrollTrigger } from 'gsap/ScrollTrigger'

gsap.registerPlugin(ScrollTrigger)

// ---- 定义概念：双栏卡片 ----
const cards = [
  {
    id: 'embodied', title: '具身智能', subtitle: 'Embodied Intelligence',
    text: '智能体依托物理身体，通过传感器获取外界信息，通过执行器与环境发生交互，并在交互过程中完成认知、学习与决策的能力。智能必须嵌入物理世界，在"感知—行动—反馈"的闭环中涌现。',
    video: '/Video Project.mp4',
  },
  {
    id: 'humanoid', title: '人形机器人', subtitle: 'Humanoid Robot',
    text: '具有类人外形结构和运动方式的机器人，由双腿、双臂、灵巧手、头部感知系统与躯干控制单元构成，能够模仿人类进行双足行走、抓取操作和自然交互，在适配现有人类环境方面具有不可替代的天然优势。',
    video: '/7e51cf20dc6145cf99ae0d0b6ea4d2c5.mp4',
  },
]

// ---- 发展历程 ----
const timeline = [
  { period: '1969 - 2000', title: '早期发展阶段', desc: '聚焦基础机械结构设计，采用刚性驱动与简单控制，仅能执行预编程的机械性重复任务。', products: ['WABOT-1'], side: 'left' },
  { period: '2000 - 2015', title: '高度集成阶段', desc: '配备视觉、力觉等丰富传感器，具备基础环境感知能力，控制算法显著提升。', products: ['ASIMO'], side: 'right' },
  { period: '2015 - 2022', title: '高动态运动阶段', desc: '攻克跑跳等复杂运动难题，初步引入深度学习与强化学习增强环境适应性。', products: ['Atlas'], side: 'left' },
  { period: '2022 - 至今', title: '具身智能爆发期', desc: '借助多模态大模型与先进算力，机器人在自然语言理解、实时自适应学习与复杂物理任务的自主决策上实现了突破性进展。', products: ['Tesla Optimus', '宇树 G1', '荣耀 闪电'], side: 'right' },
]

// ---- 机器人档案库 ----
const robots = {
  'WABOT-1': {
    name: 'WABOT-1', type: '全尺寸人形机器人',
    image: '/WABOT-1-1973.jpg',
    description: 'WABOT-1是世界上第一台全尺寸人形机器人，其研发项目于1967年在日本早稻田大学的加藤一郎实验室启动，并于1973年完成。WABOT-1身高约2米，体重160公斤，全身共有26个关节。体内搭载肢体控制系统、视觉系统和对话系统，胸部配备两个摄像头，手部安装有触觉传感器。该机器人具备基础四肢控制能力、视觉感知功能以及简单的语言交互能力，并能抓取和搬运物体。WABOT-1行动缓慢，每走一步需要45秒，步伐仅10公分左右，其智能水平被认为相当于一岁半的婴儿。',
  },
  'ASIMO': {
    name: 'ASIMO', type: '双足仿人机器人',
    image: '/Honda_ASIMO.jpg',
    description: 'ASIMO是日本本田研制的双足仿人机器人，身高1.3米，体重48公斤，最高行走速度达9km/h，2000年推出首代机型，2011年第三代机型关节自由度增至57个，采用i-WALK智能实时自由步行技术，通过陀螺仪与六轴力传感器实现平衡控制。头部双目视觉传感器支持自主避障与路线规划。2022年3月31日结束演示活动正式退役。',
  },
  'Atlas': {
    name: 'Atlas', type: '先进双足机器人',
    image: '',
    video: '/atlas.mp4',
    description: 'Atlas是由波士顿动力公司开发的先进双足机器人。最初于2013年作为液压驱动机器人推出，参与DARPA机器人挑战赛，逐渐掌握跳跃、后空翻等高难度动作。2019年通过自主步伐规划算法实现崎岖地形导航。2024年液压版退役后，电动版Atlas通过更轻量化的机械骨架和紧凑关节设计转向工业应用。',
  },
  'Tesla Optimus': {
    name: 'Tesla Optimus', type: '通用型人形机器人',
    image: '/telsa.png',
    description: '特斯拉Optimus是一款旨在代替人类执行重复及危险任务的通用型人形机器人。自2021年发布以来，它凭借特斯拉自动驾驶（FSD）同源的AI系统与行星滚柱丝杠等自研硬核执行器实现了快速迭代，并在第二代产品中展现了包括精细抓取、瑜伽平衡等复杂运动能力。当前，特斯拉正加速推进搭载全新AI5芯片的量产版本，以期实现断网环境下的本地智能闭环。',
  },
  '荣耀 闪电': {
    name: '荣耀 闪电', type: '竞速人形机器人',
    image: '/闪电.jpg',
    description: '荣耀机器人"闪电"是一款专为竞速而生的自研人形机器人。它以50分26秒的净用时夺得2026北京亦庄人形机器人半程马拉松冠军，并打破了人类半马世界纪录。"闪电"身高169厘米，采用红色机甲风设计，其核心竞争力在于速度与爆发力。它搭载了荣耀自研的一体化关节模组，峰值扭矩高达400牛·米，并配备了源自手机领域的液冷散热系统，确保了高强度奔跑下的稳定输出。',
  },
  '宇树 G1': {
    name: '宇树 G1', type: '通用人形机器人',
    image: '/宇树_G1.png',
    description: '宇树科技 G1 是一款兼具超强动态运动能力与极高性价比的通用人形机器人。自2024年发布以来，这款身高132厘米、起售价仅9.9万元的机器人，凭借其搭载的 UnifoLM 统一大模型与深度强化学习技术，在运动控制上实现了质的飞跃。G1 拥有最多43个关节自由度并配备 3D 激光雷达感知系统，完成了全球首次人形机器人侧空翻、踩墙后空翻等极高难度的动态平衡动作。从斩获世界人形机器人运动会冠军到跨界亮相春晚，宇树 G1 凭借极其强悍的软硬件结合能力极大加速了国内人形机器人在商业化与多场景融合上的落地进程。',
  },
}

// ---- 核心关键技术热点 ----
const hotspots = [
  {
    id: 'head',
    title: '环境感知与场景理解',
    label: '传感系统',
    images: ['/图片2.png', '/图片1.png'],
    desc: '视觉系统通过集成工业级 RGB-D 深度相机构建异构视觉矩阵，利用红外立体视觉实时生成高精度三维点云数据，在毫秒级延迟下执行语义 VSLAM、动态避障与微小物体识别。力觉传感通过在末端执行器、核心关节及足底嵌装六维力/力矩传感器，以微秒级高频采样率同步解算三轴受力与扭矩，结合柔顺控制与阻抗控制算法实现无损抓取与动态平衡。',
    tags: ['RGB-D 深度感知', '高精度三维点云', '六维力/力矩', '动态阻抗控制'],
    pos: { top: '8%', left: '48%' },
    lineDir: 'right',
  },
  {
    id: 'torso',
    title: '系统的"大脑"',
    label: '高性能控制器',
    images: ['/图片6.jpg'],
    desc: '主控平台采用 CPU+GPU 异构融合算力架构：CPU 负责纳秒级全局任务调度与全身动力学 WBC 控制；GPU 作为算力引擎支撑端到端多模态大模型 VLA 的极速推理。最新架构深度集成专用 AI 神经网络加速芯片 NPU/ASIC，能在几毫秒内打通从多维感知数据清洗、认知大模型自主决策到 40+ 关节指令同步下发的超级闭环。',
    tags: ['CPU+GPU异构算力', 'VLA大模型推理', '全身动力学WBC'],
    pos: { top: '32%', left: '48%' },
    lineDir: 'right',
  },
  {
    id: 'hands',
    title: '末端执行器',
    label: '末端执行器',
    images: ['/机器手.png'],
    desc: '灵巧手采用高自由度仿生设计，傅利叶 GR-2 与星动纪元"小星 Max"均具备 12 个主动自由度，可完成夹取、捏、握持、拧转等精确动作。通过软体材料与触觉反馈实现抓取力自适应调节，搭载多模态触觉传感器与电子皮肤，结合深度学习与强化学习算法，实现"手-眼-力"协同与双臂柔顺力控操作。',
    tags: ['12主动自由度', '柔性驱动', '多模态触觉', '视触觉电子皮肤'],
    pos: { top: '47%', left: '28%' },
    lineDir: 'left',
  },
  {
    id: 'frame',
    title: '核心驱动与精密传动',
    label: '电机与谐波减速器',
    images: ['/图片5.jpg', '/图片4.jpg'],
    desc: ['无框力矩电机通过海尔贝克阵列磁路与超高槽满率绕组技术大幅提升扭矩密度，Tesla Optimus 躯干旋转执行器在不到 2kg 自重下爆发 110 N·m 峰值扭矩。', '谐波减速器利用柔轮弹性变形实现 1:50 至 1:160 超大单级传动比与近乎零背隙的机械咬合，国内绿的谐波凭借 P 型三次谐波齿形将柔轮疲劳寿命突破 10,000 小时。'],
    tags: ['高扭矩密度电机', '微流道液冷', '零背隙谐波减速', '柔轮疲劳寿命'],
    pos: { top: '67%', left: '53%' },
    lineDir: 'right',
  },
]

// ---- 应用场景 ----
const scenarios = [
  {
    title: '工业领域', subtitle: 'Industrial',
    desc: '人形机器人在工业领域的优势在于其类人形态与具身智能的深度融合。凭借双足移动与灵活关节，它们能无缝融入以人为中心的复杂车间。结合多模态感知与深度学习算法，机器人具备强大的模仿学习与动态调整能力，仅需少量示教即可快速适应换产需求。以智元机器人为例，其在物料分拣、精密组装及线束插接等柔性制造任务中展现出极高的灵活性。它们不仅能接替高温高压等恶劣环境下的高危工作，保障生产安全，还能自然配合人工节奏，实现真正的人机协作。尽管当前在大规模量产的成本控制与长期运行稳定性上仍存挑战，但其在多任务处理中的高适应性，已成为推动智能制造走向柔性化、智能化的核心力量。',
    features: ['任务明确', '效率要求高', '柔性制造'],
    cases: ['物料搬运', '质检协作', '精密组装'],
    gradient: 'from-blue-700 to-cyan-600', dotColor: '#3b82f6',
    video: '/工业领域.mp4',
  },
  {
    title: '家庭服务', subtitle: 'Home Service',
    desc: '人形机器人在家庭与服务领域的应用，正逐步重塑未来的生活方式。面对高度非结构化的家庭环境与老龄化社会趋势，它们不仅能高效执行清洁、烹饪与递送等日常家务，更在情感陪伴与适老化照护中发挥核心作用，有效缓解独居老人与儿童的孤独感。以腾讯最新推出的助老机器人为例，其采用了创新的轮-腿复合式设计，完美兼顾了轮式平地移动的高效性与腿式跨越复杂地形的灵活性。依托强大的语义理解与交互能力，新一代机器人正从单纯的"家务工具"进化为具有温度的"家庭成员"，在多层次的日常辅助与照护场景中，展现出极高的环境适应力与服务潜力。',
    features: ['非结构化程度高', '任务碎片化'],
    cases: ['清洁整理', '陪伴', '烹饪'],
    gradient: 'from-emerald-700 to-teal-500', dotColor: '#10b981',
    img: '/家庭服务.png',
  },
  {
    title: '医疗领域', subtitle: 'Medical',
    desc: '"具身智能康复港"以GRx系列人形机器人为核心，融合了多模态感知、大模型、运动控制技术和康养场景需求，打造了最新的技术方案。该方案覆盖导诊咨询、上肢康复、认知康复、下肢康复和远程康复五大训练交互模块，实现康复训练、辅助照护与情感陪伴的一体化服务，打造更贴心、更智能、更高效的康复训练体验。',
    features: ['安全性要求极高', '操作精细', '多模态融合'],
    cases: ['康复训练', '病房巡检', '导诊咨询'],
    gradient: 'from-rose-700 to-pink-600', dotColor: '#f43f5e',
    img: '/医疗领域.jpg',
  },
  {
    title: '商业服务', subtitle: 'Commercial',
    desc: '擎朗智能作为全球具身服务机器人行业领军者，依托全栈自研技术体系与"研发-智造-落地"全链条闭环能力，公司持续引领服务机器人产业革新，构建了覆盖人形机器人、配送机器人、清洁机器人等行业最全品类的产品矩阵，提供前沿的具身智能解决方案。',
    features: ['重视交互体验', '形象表达', '全品类覆盖'],
    cases: ['迎宾导购', '客户接待', '配送清洁'],
    gradient: 'from-purple-700 to-violet-500', dotColor: '#a855f7',
    video: '/商业服务.mp4',
  },
  {
    title: '特种应急', subtitle: 'Special & Emergency',
    desc: '在地震废墟、有毒气体泄漏或高温火场等对人类生命构成极端威胁场景中，特种具身智能机器人展现出了无可替代的实战价值。以经典的多摆臂履带式搜救机器人为例，其独特的"主履带+独立控制摆臂"设计，使其能够轻松翻越断壁残垣、攀爬陡峭楼梯。其头部的环境感知枢纽集成了高清云台相机、红外热成像与气体探测阵列，能在浓烟或绝对黑暗中精准穿透障碍，锁定生命迹象。依托算力的提升，现代特种机器人正迅速从传统的"纯遥控操作"向"自主环境建图 (SLAM) 与自主越障规划"跨越，成为灾害现场最高效、最安全的"钢铁救援者"。',
    features: ['环境恶劣', '容错率极低'],
    cases: ['灾害救援', '消防排险'],
    gradient: 'from-orange-700 to-amber-500', dotColor: '#f97316',
    img: '/特种应急.jpg',
  },
  {
    title: '智能驾驶', subtitle: 'Intelligent Driving',
    desc: '华为乾崑 ADS 智能驾驶系统，是当前国内量产落地最广泛、技术体系最完整、用户体验最贴近真实出行场景的高阶智能驾驶解决方案，始终定位于高阶辅助驾驶，强调驾驶员全程负责与安全优先，以多传感器融合感知、端到端 AI 大模型决策、车规级高可靠控制为核心，彻底打破了传统智能驾驶对高精地图的强依赖，真正实现了全国城乡道路、高速城区、地库路面全场景贯通可用，从根本上解决了以往智能驾驶覆盖有限、场景受限、体验不稳定的行业痛点。',
    features: ['高速动态感知', '实时决策'],
    cases: ['L4 级自动驾驶', '自主导航'],
    gradient: 'from-indigo-700 to-sky-500', dotColor: '#6366f1',
    video: '/智能驾驶.mp4',
  },
]

// ---- Section 5: 未来展望 3D 翻转卡片 ----
const futureCards = [
  {
    id: 'architecture',
    category: '系统架构',
    frontTitle: '软硬件协同问题',
    frontDesc: '人形机器人系统高度复杂，涉及机械、电子、计算机等跨学科领域的深度耦合。在设计之初，如何在保证模块化、标准化以维持系统可扩展性的同时，避免接口复杂度上升导致的响应延迟，在有限的硬件条件下，实时处理海量传感器数据并完成高效计算与反馈，是目前整机协同设计的核心矛盾。',
    backPoints: [
      '架构重构：开发专为具身智能定制的异构计算平台（如融合 NPU 的边缘计算单元），优化各模块间的通信协议，减少层级调用延迟。',
      '智能资源分配：引入 AI 算法进行实时的算力与资源动态调度，在复杂任务中优先保障核心动力学（如 WBC）的计算需求。',
    ],
  },
  {
    id: 'perception',
    category: '感知与融合',
    frontTitle: '多模态感知与融合问题',
    frontDesc: '真实物理世界充满不确定性。当前机器人多依赖有限视角的单一物理量传感器，难以全面捕捉环境细节。更严峻的是，在"感知-决策-执行"的闭环中，融合视觉、力觉、触觉等多模态数据的实时性极差（主流延迟在 200~300ms），低于 100ms 的高鲁棒性实时响应尚未全面实现，导致动态环境下的操作极易失败。',
    backPoints: [
      '仿生多维传感：加速研发具备温度、压力、纹理多重感知能力的高集成度仿生电子皮肤，以及广角与激光雷达融合的新型主动视觉云台，补全信息盲区。',
      '高效融合算法：开发基于深度学习和强化学习的新一代多模态融合算法。通过硬件加速（如专用 DSP/NPU）和算法层面的特征级融合，大幅降低计算冗余，突破 100ms 的实时性瓶颈。',
    ],
  },
  {
    id: 'decision',
    category: '决策与泛化',
    frontTitle: '强思维链与高泛化性问题',
    frontDesc: '在未知、高度动态的环境中，机器人极度缺乏实时、准确的环境建模能力，导致物理模型更新滞后。同时，受限于有限的计算能力和多模态融合精度，机器人难以建立强大的"思维链"，面对非标任务（如异型件的灵巧抓取与组装）时，决策速度慢、操作稳定性差、泛化能力弱。',
    backPoints: [
      '跨越数据鸿沟：建立高质量的、符合真实物理规律的大规模"感知与操作数据集"，通过先进的 Sim-to-Real（仿真到现实）技术，实现虚拟训练成果向物理世界的无损迁移。',
      '端到端大模型：部署视觉-语言-动作 (VLA) 端到端大模型，赋予机器人强大的逻辑推理与"思维链"能力，在未见过的环境中实现零样本的快速探索与自适应决策。',
    ],
  },
  {
    id: 'energy',
    category: '动力与能源',
    frontTitle: '动力驱动与长续航问题',
    frontDesc: '人形机器人要在全场景落地，必须兼顾高爆发力与长续航。然而，现有电池在能量密度和充电效率上进展缓慢，成为沉重的物理枷锁。同时，在复杂地形中维持平稳步态和动态平衡，会产生巨大的额外能耗，当前基于深度学习的步态控制模型在自适应能力和计算效率上仍无法满足"高效低耗"的要求。',
    backPoints: [
      '硬件革新：加速高能量密度固态电池的商业化应用，降低温度敏感性，延长高负荷下的极限输出时间；探索半刚性/柔性驱动器（如人工肌肉）以提高基础机械效率。',
      '智能能耗管理：引入基于强化学习的全局能量管理系统。通过算法实现能耗的动态调节、高效率的制动能量回收，以及针对不同任务的"智能能耗模式无缝切换"。',
    ],
  },
]

const flippedCards = ref({})
function toggleFutureFlip(id) {
  flippedCards.value = { ...flippedCards.value, [id]: !flippedCards.value[id] }
}

// ---- 卡片展开模态 ----
const activeCard = ref(null)
const openCard = (c) => { activeCard.value = c }
const closeCard = () => { activeCard.value = null }

// ---- Section 3 激活状态（null = 全部折叠） ----
const activeScenario = ref(null)

// ---- 全局鼠标坐标 ----
const mouseCoord = ref({ x: 0, y: 0 })
function onGlobalMouseMove(e) {
  mouseCoord.value = { x: e.clientX, y: e.clientY }
}

// ---- 磁性吸附 ----
function magneticHover(e, el) {
  const rect = el.getBoundingClientRect()
  const cx = rect.left + rect.width / 2
  const cy = rect.top + rect.height / 2
  const dx = (e.clientX - cx) * 0.15
  const dy = (e.clientY - cy) * 0.15
  gsap.to(el, { x: dx, y: dy, duration: 0.45, ease: 'power2.out' })
}
function magneticLeave(el) {
  gsap.to(el, { x: 0, y: 0, duration: 0.7, ease: 'elastic.out(1, 0.4)' })
}

// ---- Section 4 热点激活 & 模态 ----
const activeRobotTech = ref(null)
const mouseParallax = ref({ x: 0, y: 0 })

function openTechModal(hp) {
  activeRobotTech.value = hp
  nextTick(() => {
    const overlay = document.querySelector('.tech-modal-overlay')
    const panel = document.querySelector('.tech-modal-panel')
    if (overlay) gsap.fromTo(overlay, { opacity: 0 }, { opacity: 1, duration: 0.35, ease: 'power2.out' })
    if (panel) gsap.fromTo(panel, { scale: 0.8, y: 40, opacity: 0 }, { scale: 1, y: 0, opacity: 1, duration: 0.55, ease: 'back.out(1.4)' })
    const tags = document.querySelectorAll('.tech-modal-panel .tech-tag')
    if (tags.length) {
      gsap.fromTo(tags, { opacity: 0, y: 12, scale: 0.7 }, { opacity: 1, y: 0, scale: 1, duration: 0.35, stagger: 0.05, ease: 'back.out(1.4)', delay: 0.3 })
    }
  })
}

function closeTechModal() {
  const overlay = document.querySelector('.tech-modal-overlay')
  const panel = document.querySelector('.tech-modal-panel')
  if (panel) gsap.to(panel, { scale: 0.85, y: 30, opacity: 0, duration: 0.3, ease: 'power2.in' })
  if (overlay) gsap.to(overlay, { opacity: 0, duration: 0.3, ease: 'power2.in', delay: 0.05 })
  setTimeout(() => { activeRobotTech.value = null }, 350)
}

function onScanMouseMove(e) {
  const rect = e.currentTarget.getBoundingClientRect()
  const x = (e.clientX - rect.left) / rect.width - 0.5
  const y = (e.clientY - rect.top) / rect.height - 0.5
  mouseParallax.value = { x, y }
}

function onScanMouseLeave() {
  mouseParallax.value = { x: 0, y: 0 }
}

// ---- 机器人档案模态 ----
const selectedRobot = ref(null)
const isRobotModalOpen = ref(false)
const isFlipped = ref(false)

function openRobotModal(name) {
  if (!robots[name]) return
  selectedRobot.value = robots[name]
  isFlipped.value = false
  isRobotModalOpen.value = true
  // 动画入口在 watch/transition hooks 中处理
  nextTick(() => {
    const card = document.querySelector('.robot-flip-card')
    const overlay = document.querySelector('.robot-modal-overlay')
    if (overlay) gsap.fromTo(overlay, { opacity: 0 }, { opacity: 1, duration: 0.35, ease: 'power2.out' })
    if (card) gsap.fromTo(card, { scale: 0.5, y: 50, opacity: 0 }, { scale: 1, y: 0, opacity: 1, duration: 0.55, ease: 'back.out(1.4)' })
  })
}

function closeRobotModal() {
  const card = document.querySelector('.robot-flip-card')
  const overlay = document.querySelector('.robot-modal-overlay')
  if (card) gsap.to(card, { scale: 0.85, y: 30, opacity: 0, duration: 0.3, ease: 'power2.in' })
  if (overlay) gsap.to(overlay, { opacity: 0, duration: 0.3, ease: 'power2.in', delay: 0.05 })
  setTimeout(() => {
    isRobotModalOpen.value = false
    selectedRobot.value = null
    isFlipped.value = false
  }, 350)
}

function onKeydown(e) {
  if (e.key === 'Escape') {
    if (activeRobotTech.value) {
      closeTechModal()
    } else if (isRobotModalOpen.value) {
      closeRobotModal()
    } else if (activeCard.value) {
      activeCard.value = null
    }
  }
}

onMounted(() => {
  window.addEventListener('keydown', onKeydown)
  window.addEventListener('mousemove', onGlobalMouseMove)
  nextTick(() => {
    // Hero
    const heroTitle = document.querySelector('#hero .hero-anim-title')
    const heroTags = document.querySelectorAll('#hero .hero-anim-tag')
    if (heroTitle) gsap.fromTo(heroTitle, { opacity: 0, y: 40 }, { opacity: 1, y: 0, duration: 0.6, ease: 'power3.out' })
    if (heroTags.length) gsap.fromTo(heroTags, { opacity: 0, y: 20 }, { opacity: 1, y: 0, duration: 0.35, stagger: 0.06, ease: 'power2.out', delay: 0.2 })

    // Section 1 — 区块标题 + 卡片交错入场
    const secConcept = document.querySelector('#sec-concept')
    if (secConcept) {
      const title = secConcept.querySelector('.section-title')
      const items = secConcept.querySelectorAll('.section-item')
      let played = false
      const observer = new IntersectionObserver((entries) => {
        entries.forEach((entry) => {
          if (entry.isIntersecting && !played) {
            played = true
            if (title) gsap.fromTo(title, { opacity: 0, y: 30 }, { opacity: 1, y: 0, duration: 0.5, ease: 'power3.out' })
            if (items.length) gsap.fromTo(items, { opacity: 0, y: 24 }, { opacity: 1, y: 0, duration: 0.35, stagger: 0.06, ease: 'power2.out', delay: 0.1 })
          } else if (!entry.isIntersecting && played) {
            played = false
            if (title) gsap.set(title, { opacity: 0, y: 30 })
            if (items.length) gsap.set(items, { opacity: 0, y: 24 })
          }
        })
      }, { threshold: 0.1 })
      observer.observe(secConcept)
      sectionObservers.push(observer)
    }

    // Section 2 — 滚动依次出现
    initTimelineScroll()

    // Section 2 标题动画
    const secTimeline = document.querySelector('#sec-timeline')
    if (secTimeline) {
      const title = secTimeline.querySelector('.section-title')
      let played = false
      const observer = new IntersectionObserver((entries) => {
        entries.forEach((entry) => {
          if (entry.isIntersecting && !played) {
            played = true
            if (title) gsap.fromTo(title, { opacity: 0, y: 30 }, { opacity: 1, y: 0, duration: 0.5, ease: 'power3.out' })
          } else if (!entry.isIntersecting && played) {
            played = false
            if (title) gsap.set(title, { opacity: 0, y: 30 })
          }
        })
      }, { threshold: 0.1 })
      observer.observe(secTimeline)
      sectionObservers.push(observer)
    }

    // Section 3 — 点击展开手风琴
    initAccordion()

    // Section 4 — 热点呼吸浮动 & 扫描显现
    initHotspots()
    const secTech = document.querySelector('#sec-tech')
    if (secTech) {
      const title = secTech.querySelector('.section-title')
      const img = secTech.querySelector('.scan-image')
      let scanPlayed = false
      const observer = new IntersectionObserver((entries) => {
        entries.forEach((entry) => {
          if (entry.isIntersecting && !scanPlayed) {
            scanPlayed = true
            if (title) gsap.fromTo(title, { opacity: 0, y: 30 }, { opacity: 1, y: 0, duration: 0.5, ease: 'power3.out' })
            if (img) img.classList.add('scan-reveal')
          }
        })
      }, { threshold: 0.15 })
      observer.observe(secTech)
      sectionObservers.push(observer)
    }

    // Section 5 — 未来展望卡片入场
    const secFuture = document.querySelector('#sec-future')
    if (secFuture) {
      const title = secFuture.querySelector('.section-title')
      const cards = secFuture.querySelectorAll('.future-card')
      let played = false
      const observer = new IntersectionObserver((entries) => {
        entries.forEach((entry) => {
          if (entry.isIntersecting && !played) {
            played = true
            if (title) gsap.fromTo(title, { opacity: 0, y: 30 }, { opacity: 1, y: 0, duration: 0.5, ease: 'power3.out' })
            if (cards.length) gsap.fromTo(cards, { opacity: 0, y: 50 }, { opacity: 1, y: 0, duration: 0.6, stagger: 0.15, ease: 'back.out(1.7)', delay: 0.1 })
          }
        })
      }, { threshold: 0.1 })
      observer.observe(secFuture)
      sectionObservers.push(observer)
    }

    // 结语 — 沉稳淡入 + 粒子
    const secOutro = document.querySelector('#sec-outro')
    if (secOutro) {
      const items = secOutro.querySelectorAll('.outro-stagger')
      let outroPlayed = false
      const observer = new IntersectionObserver((entries) => {
        entries.forEach((entry) => {
          if (entry.isIntersecting && !outroPlayed) {
            outroPlayed = true
            if (items.length) gsap.fromTo(items, { opacity: 0, y: 30 }, { opacity: 1, y: 0, duration: 1.5, stagger: 0.3, ease: 'power2.out' })
            initParticles()
          }
        })
      }, { threshold: 0.15 })
      observer.observe(secOutro)
      sectionObservers.push(observer)
    }

    // 粒子系统 — 延迟初始化确保 DOM 就绪
    setTimeout(() => initParticles(), 500)

    ScrollTrigger.refresh()
  })
})

let sectionObservers = []
let tlObservers = []

function initTimelineScroll() {
  const nodes = document.querySelectorAll('#sec-timeline .tl-node')
  if (!nodes.length) return

  nodes.forEach((node) => {
    gsap.set(node, { opacity: 0, y: 30 })
    const observer = new IntersectionObserver((entries) => {
      entries.forEach((entry) => {
        if (entry.isIntersecting) {
          gsap.to(entry.target, { opacity: 1, y: 0, duration: 0.5, ease: 'power3.out' })
        } else {
          gsap.to(entry.target, { opacity: 0, y: 30, duration: 0.3 })
        }
      })
    }, { threshold: 0.15 })
    observer.observe(node)
    tlObservers.push(observer)
  })
}

// ---- Section 3 手风琴逻辑 ----
function initAccordion() {
  const accordionCards = document.querySelectorAll('#sec-scenarios .accordion-card')
  if (!accordionCards.length) return
  resetAccordion()

  accordionCards.forEach((card, idx) => {
    card.addEventListener('click', () => {
      if (activeScenario.value === idx) {
        activeScenario.value = null
        resetAccordion()
      } else {
        activeScenario.value = idx
        applyAccordion(idx)
      }
    })
  })
}

function resetAccordion() {
  const cards = document.querySelectorAll('#sec-scenarios .accordion-card')
  const equal = 100 / cards.length
  cards.forEach((card) => {
    const detail = card.querySelector('.card-detail')
    const titleV = card.querySelector('.card-title-v')
    gsap.to(card, { flex: `0 0 ${equal}%`, duration: 0.55, ease: 'power3.out' })
    if (detail) gsap.to(detail, { opacity: 0, y: 16, duration: 0.3, ease: 'power2.in' })
    if (titleV) gsap.to(titleV, { opacity: 1, duration: 0.3, delay: 0.2 })
  })
}

function applyAccordion(activeIdx) {
  const cards = document.querySelectorAll('#sec-scenarios .accordion-card')
  const total = cards.length
  const expandFlex = 75
  const shrinkFlex = (100 - expandFlex) / (total - 1)
  cards.forEach((card, idx) => {
    const detail = card.querySelector('.card-detail')
    const titleV = card.querySelector('.card-title-v')
    if (idx === activeIdx) {
      gsap.to(card, { flex: `0 0 ${expandFlex}%`, duration: 0.55, ease: 'power3.out' })
      if (detail) {
        gsap.to(detail, { opacity: 1, y: 0, duration: 0.4, ease: 'power3.out', delay: 0.25 })
        const children = detail.querySelectorAll('.stagger-item')
        if (children.length) {
          gsap.fromTo(children, { opacity: 0, y: 20 }, { opacity: 1, y: 0, duration: 0.35, stagger: 0.06, ease: 'power2.out', delay: 0.35 })
        }
      }
      if (titleV) gsap.to(titleV, { opacity: 0, duration: 0.2 })
    } else {
      gsap.to(card, { flex: `0 0 ${shrinkFlex}%`, duration: 0.55, ease: 'power3.out' })
      if (detail) gsap.to(detail, { opacity: 0, y: 16, duration: 0.3, ease: 'power2.in' })
      if (titleV) gsap.to(titleV, { opacity: 1, duration: 0.3, delay: 0.2 })
    }
  })
}

// ---- Section 4 热点动画 ----
function initHotspots() {
  const groups = document.querySelectorAll('#sec-tech .hotspot-group')
  groups.forEach((group) => {
    const ring = group.querySelector('.hotspot-ring')
    if (ring) {
      gsap.to(ring, { scale: 1.8, opacity: 0, duration: 2, repeat: -1, ease: 'sine.out' })
    }
  })
}

// ---- Canvas 粒子系统 ----
let particleRaf = null
let particleCanvas = null
let particleCtx = null
let particles = []

function createParticles(canvas) {
  const count = 120
  particles = []
  for (let i = 0; i < count; i++) {
    particles.push({
      x: Math.random() * canvas.width,
      y: Math.random() * canvas.height,
      r: Math.random() * 1.8 + 0.5,
      speed: Math.random() * 0.55 + 0.2,
      wobble: Math.random() * 0.5 - 0.25,
      opacity: Math.random() * 0.55 + 0.1,
      phase: Math.random() * Math.PI * 2,
    })
  }
}

function animateParticles() {
  if (!particleCtx || !particleCanvas) return
  const ctx = particleCtx
  const w = particleCanvas.width
  const h = particleCanvas.height

  ctx.clearRect(0, 0, w, h)

  for (const p of particles) {
    p.y -= p.speed
    p.x += Math.sin(p.phase + performance.now() * 0.0005) * p.wobble

    if (p.y < -10) { p.y = h + 10; p.x = Math.random() * w }
    if (p.x < -10) p.x = w + 10
    if (p.x > w + 10) p.x = -10

    const twinkle = 0.5 + 0.5 * Math.sin(performance.now() * 0.003 + p.phase)
    const alpha = p.opacity * (0.5 + 0.5 * twinkle)

    ctx.beginPath()
    ctx.arc(p.x, p.y, p.r, 0, Math.PI * 2)
    ctx.fillStyle = `rgba(147,197,253,${alpha.toFixed(2)})`
    ctx.fill()
  }

  particleRaf = requestAnimationFrame(animateParticles)
}

function initParticles() {
  const canvas = document.querySelector('#particle-canvas')
  if (!canvas) return
  particleCanvas = canvas
  canvas.width = canvas.offsetWidth || canvas.clientWidth || window.innerWidth
  canvas.height = canvas.offsetHeight || canvas.clientHeight || window.innerHeight
  particleCtx = canvas.getContext('2d')
  createParticles(canvas)
  if (!particleRaf) animateParticles()
}

function destroyParticles() {
  if (particleRaf) { cancelAnimationFrame(particleRaf); particleRaf = null }
  particleCtx = null
  particleCanvas = null
  particles = []
}

onBeforeUnmount(() => {
  window.removeEventListener('keydown', onKeydown)
  window.removeEventListener('mousemove', onGlobalMouseMove)
  sectionObservers.forEach(o => o.disconnect())
  tlObservers.forEach(o => o.disconnect())
  destroyParticles()
})
</script>

<template>
  <div class="snap-container h-screen overflow-y-scroll snap-y snap-mandatory bg-gray-950 text-white">

    <!-- ====================== HERO ====================== -->
    <section id="hero" class="h-screen w-full snap-start flex flex-col items-center justify-center relative overflow-hidden">
      <div class="hero-glow"></div>
      <div class="z-10 text-center px-6">
        <h1 class="hero-anim-title text-5xl sm:text-6xl md:text-7xl lg:text-8xl font-black tracking-tighter leading-[0.9]">
          <span class="hero-title-text">具身智能</span>
          <span class="hero-title-connector hidden sm:inline">·</span>
          <span class="hero-title-text hero-title-delay">人形机器人</span>
        </h1>
        <div class="hero-anim-tag mt-8 flex flex-wrap justify-center gap-3">
          <span v-for="ch in ['定义概念', '发展历程', '应用场景', '关键技术', '未来展望']" :key="ch"
            class="px-5 py-2.5 rounded-full text-sm border border-white/10 bg-white/5 backdrop-blur text-slate-200">
            {{ ch }}
          </span>
        </div>
      </div>
      <div class="hero-anim-tag absolute bottom-8 left-1/2 -translate-x-1/2 flex flex-col items-center gap-2 text-slate-500">
        <span class="text-xs tracking-[0.2em] uppercase">Scroll</span>
        <div class="w-5 h-8 rounded-full border border-slate-600 flex justify-center pt-1.5">
          <div class="w-1 h-2 rounded-full bg-slate-500 animate-bounce"></div>
        </div>
      </div>
    </section>

    <!-- ====================== SECTION 1: 概念与定义 ====================== -->
    <section id="sec-concept"
      class="snap-section min-h-screen w-full snap-start flex flex-col justify-center px-6 sm:px-10 md:px-16 py-20">
      <div class="section-title mb-8 md:mb-12">
        <span class="text-xs sm:text-sm tracking-[0.3em] uppercase text-cyan-400/80">Section 01</span>
        <h2 class="text-3xl sm:text-4xl md:text-5xl font-bold mt-1 tracking-tight">
          <span class="bg-gradient-to-r from-cyan-300 via-blue-400 to-purple-400 bg-clip-text text-transparent">概念与定义</span>
        </h2>
        <div class="mt-3 w-20 h-0.5 bg-gradient-to-r from-cyan-400 to-transparent rounded-full"></div>
      </div>
      <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
        <article v-for="card in cards" :key="card.id"
          class="section-item card-hover group relative rounded-3xl overflow-hidden cursor-pointer
                 border border-white/5 bg-gray-900 transition-all duration-300 min-h-[380px]"
          @click="openCard(card)">
          <video class="absolute inset-0 w-full h-full object-cover" autoplay loop muted playsinline :src="card.video"></video>
          <div class="absolute inset-0 bg-gradient-to-t from-gray-950 via-gray-950/40 to-transparent"></div>
          <div class="absolute inset-x-0 bottom-0 p-6 sm:p-8 z-10">
            <h3 class="text-2xl sm:text-4xl font-bold tracking-tight">{{ card.title }}</h3>
            <p class="text-sm sm:text-base text-slate-400 mt-1">{{ card.subtitle }}</p>
            <p class="mt-3 text-sm sm:text-base text-slate-200 leading-relaxed">{{ card.text }}</p>
          </div>
        </article>
      </div>
    </section>

    <!-- ====================== SECTION 2: 发展历程 ====================== -->
    <section id="sec-timeline"
      class="snap-section min-h-screen w-full snap-start flex flex-col justify-center px-6 sm:px-10 md:px-16 py-20">
      <div class="section-title mb-10 md:mb-14">
        <span class="text-xs sm:text-sm tracking-[0.3em] uppercase text-cyan-400/80">Section 02</span>
        <h2 class="text-3xl sm:text-5xl md:text-6xl font-bold mt-1 tracking-tight">
          <span class="bg-gradient-to-r from-cyan-300 via-blue-400 to-purple-400 bg-clip-text text-transparent">发展历程</span>
        </h2>
        <div class="mt-3 w-20 h-0.5 bg-gradient-to-r from-cyan-400 to-transparent rounded-full"></div>
      </div>

      <div class="relative max-w-4xl mx-auto pb-12">
        <div class="absolute left-5 md:left-1/2 md:-translate-x-px top-2 bottom-4 w-px bg-gradient-to-b from-cyan-500/80 via-blue-500/60 to-purple-500/40"></div>
        <div class="flex flex-col">
          <div v-for="(node, i) in timeline" :key="node.period"
            class="tl-node relative pl-12 md:pl-0 py-5 md:py-8"
            :class="['md:w-[46%]', node.side === 'left' ? 'md:pr-10 md:ml-0 md:mr-auto' : 'md:pl-10 md:ml-auto md:mr-0']">
            <div class="absolute top-7 md:top-10 w-5 h-5 rounded-full z-10 ring-4 ring-gray-950
                        bg-gradient-to-br from-cyan-400 to-blue-500 shadow-[0_0_18px_rgba(34,211,238,0.55)]"
              :class="['left-[5px]', node.side === 'left' ? 'md:left-auto md:-right-2.5' : 'md:-left-2.5']"></div>
            <div class="mb-3 text-base sm:text-lg font-bold font-mono tracking-wide"
              :class="node.side === 'left' ? 'text-cyan-400 md:text-right' : 'text-blue-400 md:text-left'">
              {{ node.period }}
            </div>
            <div class="p-5 sm:p-6 rounded-2xl border border-white/10 bg-gray-900/60 backdrop-blur
                        transition-all duration-300 hover:border-white/20">
              <h3 class="text-lg sm:text-xl font-bold bg-gradient-to-r from-cyan-300 to-blue-400 bg-clip-text text-transparent">
                {{ node.title }}
              </h3>
              <p class="mt-2 text-sm sm:text-base text-slate-200 leading-relaxed">{{ node.desc }}</p>
              <div class="mt-3 flex flex-wrap gap-2">
                <span v-for="p in node.products" :key="p"
                  class="px-3 py-1.5 rounded-full text-sm font-medium cursor-pointer
                         bg-blue-900/30 text-blue-300 border border-blue-800/60
                         transition-all duration-300 hover:border-blue-500/60 hover:translate-y-[-1px]
                         hover:bg-blue-800/40 hover:text-blue-200 hover:shadow-[0_0_16px_rgba(59,130,246,0.35)]"
                  @click.stop="openRobotModal(p)">
                  {{ p }}
                </span>
              </div>
            </div>
          </div>
        </div>
      </div>
    </section>

    <!-- ====================== SECTION 3: 应用场景 · 点击展开手风琴 ====================== -->
    <section id="sec-scenarios"
      class="min-h-screen w-full snap-start flex flex-col bg-gray-950">
      <div class="flex-shrink-0 px-8 md:px-16 pt-10 pb-4">
        <span class="text-xs sm:text-sm tracking-[0.3em] uppercase text-cyan-400/80">Section 03</span>
        <h2 class="text-3xl sm:text-5xl md:text-6xl font-bold mt-1 tracking-tight">
          <span class="bg-gradient-to-r from-cyan-300 via-blue-400 to-purple-400 bg-clip-text text-transparent">应用场景</span>
        </h2>
        <div class="mt-3 w-20 h-0.5 bg-gradient-to-r from-cyan-400 to-transparent rounded-full"></div>
      </div>

      <div class="accordion-container flex-1 flex flex-row gap-2 px-8 md:px-16 pt-2 pb-10 min-h-[90vh]">
        <div v-for="(s, idx) in scenarios" :key="s.title"
          class="accordion-card relative overflow-hidden rounded-2xl cursor-pointer flex-shrink-0
                 transition-transform duration-300"
          :class="activeScenario !== idx ? 'hover:-translate-y-2 hover:shadow-[0_0_40px_rgba(56,189,248,0.25)] hover:brightness-110' : ''"
          style="flex: 0 0 16.666%;"
        >
          <div :class="['absolute inset-0 bg-gradient-to-b', s.gradient]"></div>
          <video v-if="s.video" class="absolute inset-0 w-full h-full object-cover" autoplay loop muted playsinline :src="s.video"></video>
          <img v-if="s.img" class="absolute inset-0 w-full h-full object-cover" :src="s.img" />

          <!-- 折叠时遮罩，展开时完全移除 -->
          <div v-if="activeScenario !== idx" class="absolute inset-0 bg-black/70 transition-all duration-500"></div>

          <div class="card-title-v absolute inset-0 flex items-center justify-center pointer-events-none">
            <span class="text-white text-xl sm:text-2xl md:text-3xl font-black tracking-[0.15em] whitespace-nowrap"
              style="writing-mode: vertical-rl; text-orientation: mixed;">
              {{ s.title }}
            </span>
          </div>

          <div class="card-detail absolute inset-y-0 left-0 w-[42%] max-w-lg h-full top-0
                      bg-gray-950/85 backdrop-blur-md border-r border-gray-700/50 shadow-2xl
                      flex flex-col justify-center opacity-0 translate-y-5 pointer-events-none p-8 md:p-10 overflow-y-auto scrollbar-none">
            <p class="stagger-item text-emerald-400 text-xs tracking-[0.2em] font-bold mb-2 uppercase">Application Scenario</p>
            <h3 class="stagger-item text-4xl md:text-5xl font-black text-white mb-6 tracking-tight">{{ s.title }}</h3>
            <div class="stagger-item h-1 w-12 bg-blue-500 rounded-full mb-8 shadow-[0_0_10px_rgba(59,130,246,0.8)]"></div>
            <p v-if="s.desc" class="stagger-item text-xs sm:text-sm text-gray-200 leading-relaxed mb-6">{{ s.desc }}</p>
            <div class="stagger-item">
              <p class="text-xs tracking-[0.2em] uppercase text-gray-500 mb-3">Core Use Cases</p>
              <div class="flex flex-wrap gap-2.5">
                <span v-for="c in s.cases" :key="c"
                  class="px-3 py-1.5 text-sm rounded-md border border-gray-600 bg-black/40 text-gray-300 shadow-inner">
                  {{ c }}
                </span>
              </div>
            </div>
          </div>
        </div>
      </div>
    </section>

    <!-- ====================== SECTION 4: 核心关键技术 · 全息扫描 ====================== -->
    <section id="sec-tech"
      class="snap-section min-h-screen w-full snap-start flex flex-col justify-center px-6 sm:px-10 md:px-16 py-12 bg-gray-950">
      <div class="section-title mb-6 md:mb-10">
        <span class="text-xs sm:text-sm tracking-[0.3em] uppercase text-cyan-400/80">Section 04</span>
        <h2 class="text-3xl sm:text-5xl md:text-6xl font-bold mt-1 tracking-tight">
          <span class="bg-gradient-to-r from-cyan-300 via-blue-400 to-purple-400 bg-clip-text text-transparent">核心关键技术</span>
        </h2>
        <div class="mt-3 w-20 h-0.5 bg-gradient-to-r from-cyan-400 to-transparent rounded-full"></div>
      </div>

      <!-- 全息扫描台 -->
      <div class="relative w-full max-w-4xl mx-auto flex-1 flex items-center justify-center min-h-[70vh]">
        <div class="relative w-full h-full flex items-center justify-center">
          <!-- 机器人透视图 · 全息悬浮 -->
          <div class="animate-float pointer-events-none">
            <img class="scan-image h-[80vh] w-auto object-contain drop-shadow-[0_0_25px_rgba(59,130,246,0.5)] brightness-110" src="/背景图.png" alt="人形机器人透视图" />
          </div>

          <!-- 热点层 -->
          <div class="absolute inset-0">
            <div v-for="hp in hotspots" :key="hp.id"
              class="hotspot-group absolute z-10 flex items-center"
              :class="hp.lineDir === 'left' ? 'flex-row-reverse' : ''"
              :style="{ top: hp.pos.top, left: hp.pos.left }">
              <!-- 呼吸光环 + 核心圆点 -->
              <div class="relative w-4 h-4 flex-shrink-0">
                <div class="hotspot-ring absolute -inset-2 rounded-full border border-blue-400/40 bg-blue-500/10"></div>
                <div class="hotspot-dot relative w-4 h-4 rounded-full bg-blue-400
                            shadow-[0_0_24px_rgba(56,189,248,0.9),0_0_48px_rgba(56,189,248,0.4)]
                            border border-white/60"></div>
              </div>
              <!-- 连接线 -->
              <div class="w-8 sm:w-10 h-px flex-shrink-0"
                :class="hp.lineDir === 'left' ? 'bg-gradient-to-l from-blue-400/50 to-transparent' : 'bg-gradient-to-r from-blue-400/50 to-transparent'"></div>
              <!-- 药丸标签 -->
              <span class="flex-shrink-0 px-3 py-1.5 rounded-full bg-white/90 text-gray-900 text-[10px] sm:text-xs font-bold tracking-wider
                         cursor-pointer hover:bg-white hover:shadow-[0_0_20px_rgba(255,255,255,0.3)] transition-all duration-300
                         whitespace-nowrap select-none"
                @click.stop="openTechModal(hp)">
                {{ hp.label }}
              </span>
            </div>
          </div>
        </div>
      </div>

      <!-- ===== 技术档案全屏模态 ===== -->
      <div v-if="activeRobotTech"
        class="tech-modal-overlay fixed inset-0 z-[55] flex items-center justify-center p-4 sm:p-8 bg-black/80 backdrop-blur-xl"
        @click="closeTechModal">
        <div class="tech-modal-panel bg-gray-900/85 backdrop-blur-md border border-blue-500/30 p-8 sm:p-10 rounded-2xl max-w-3xl w-full max-h-[85vh] overflow-y-auto scrollbar-none
                    shadow-[0_0_80px_rgba(59,130,246,0.15)]"
          @click.stop>
          <div class="flex gap-8">
            <!-- 左侧文字 -->
            <div class="flex-1 min-w-0">
              <h3 class="text-3xl sm:text-4xl font-extrabold text-white mb-3 tracking-tight"
                style="text-shadow: 0 0 24px rgba(59,130,246,0.4);">
                {{ activeRobotTech.title }}
              </h3>
              <div class="h-px w-20 bg-gradient-to-r from-blue-500 to-transparent mb-5"></div>
              <template v-if="Array.isArray(activeRobotTech.desc)">
                <p v-for="(d, i) in activeRobotTech.desc" :key="i" class="text-base text-gray-200 leading-relaxed mb-4">{{ d }}</p>
              </template>
              <p v-else class="text-base text-gray-200 leading-relaxed mb-4">{{ activeRobotTech.desc }}</p>
              <div class="flex flex-wrap gap-3">
                <span v-for="tag in activeRobotTech.tags" :key="tag"
                  class="tech-tag px-4 py-2 text-base rounded-full border border-blue-500/40 bg-blue-950/30 text-blue-100
                         shadow-[0_0_12px_rgba(59,130,246,0.15)]">
                  {{ tag }}
                </span>
              </div>
            </div>
            <!-- 右侧图片 -->
            <div v-if="activeRobotTech.images?.length" class="flex flex-col gap-3 w-48 sm:w-56 flex-shrink-0">
              <img v-for="(img, i) in activeRobotTech.images" :key="i"
                class="w-full rounded-xl object-cover border border-gray-700/50 shadow-lg"
                :src="img" />
            </div>
          </div>
        </div>
      </div>
    </section>

    <!-- ====================== SECTION 5: 未来展望 · 3D 翻转卡片 ====================== -->
    <section id="sec-future"
      class="snap-section min-h-screen w-full snap-start flex flex-col justify-center px-6 sm:px-10 md:px-16 py-16 bg-gray-950">
      <div class="section-title mb-10 md:mb-14">
        <span class="text-xs sm:text-sm tracking-[0.3em] uppercase text-cyan-400/80">Section 05</span>
        <h2 class="text-3xl sm:text-5xl md:text-6xl font-bold mt-1 tracking-tight">
          <span class="bg-gradient-to-r from-cyan-300 via-blue-400 to-purple-400 bg-clip-text text-transparent">未来展望</span>
        </h2>
        <div class="mt-3 w-20 h-0.5 bg-gradient-to-r from-cyan-400 to-transparent rounded-full"></div>
      </div>

      <div class="grid grid-cols-1 md:grid-cols-2 gap-8 max-w-6xl mx-auto w-full">
        <div v-for="fc in futureCards" :key="fc.id"
          class="future-card future-card-perspective h-[420px] cursor-pointer"
          :class="{ flipped: flippedCards[fc.id] }"
          @click="toggleFutureFlip(fc.id)">
          <div class="future-card-inner relative w-full h-full">
            <!-- ===== 正面 ===== -->
            <div class="future-card-front absolute inset-0 rounded-2xl overflow-hidden
                      bg-gray-900/80 backdrop-blur border border-orange-500/30
                      p-6 sm:p-8 flex flex-col justify-center
                      shadow-[0_0_40px_rgba(249,115,22,0.06)]">
              <h3 class="text-2xl sm:text-3xl font-bold text-amber-200 mb-5 leading-snug tracking-tight">{{ fc.frontTitle }}</h3>
              <p class="text-base sm:text-lg text-gray-300 leading-relaxed">{{ fc.frontDesc }}</p>
            </div>
            <!-- ===== 背面 ===== -->
            <div class="future-card-back absolute inset-0 rounded-2xl overflow-hidden
                      bg-gray-900/80 backdrop-blur border border-cyan-400/50
                      p-6 sm:p-8 flex flex-col justify-center
                      shadow-[0_0_50px_rgba(34,211,238,0.08)]">
              <div class="space-y-5">
                <div v-for="(pt, pi) in fc.backPoints" :key="pi"
                  class="flex gap-3 items-start">
                  <span class="flex-shrink-0 w-7 h-7 rounded-full bg-cyan-500/10 border border-cyan-500/30 flex items-center justify-center text-sm text-cyan-400 font-bold mt-0.5">
                    {{ pi + 1 }}
                  </span>
                  <p class="text-base sm:text-lg text-gray-200 leading-relaxed">{{ pt }}</p>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </section>

    <!-- ====================== 结语 ====================== -->
    <section id="sec-outro"
      class="snap-section min-h-screen w-full snap-start flex flex-col items-center justify-center bg-[#020617] relative overflow-hidden">
      <canvas id="particle-canvas" class="absolute inset-0 w-full h-full pointer-events-none"></canvas>
      <div class="outro-stagger max-w-4xl mx-auto px-6 py-24 sm:py-32 text-center relative z-10">
        <div class="outro-stagger w-12 h-[1px] bg-blue-500/50 shadow-[0_0_8px_rgba(59,130,246,0.8)] mx-auto mb-8"></div>
        <p class="outro-stagger text-xl sm:text-2xl text-blue-100/80 leading-loose tracking-wide mb-8">
          人形机器人的发展，是一场跨越机械、电子、材料与人工智能的史诗级技术长征。它不仅代表着科技的最前沿，更是重塑未来产业格局的核心驱动力。
        </p>
        <p class="outro-stagger text-xl sm:text-2xl text-blue-100/80 leading-loose tracking-wide">
          面对极其复杂的现实世界，虽然目前仍然存在环境感知、灵巧操作与高泛化决策等技术壁垒，但"端到端大模型+具身智能"的新范式已然指明了方向。人形机器人必将成为"新质生产力"的典型代表，为人类社会注入无尽的想象力。
        </p>
      </div>
    </section>

    <!-- ====================== 概念卡片展开模态 ====================== -->
    <Transition name="modal-fade">
      <div v-if="activeCard" class="fixed inset-0 z-50 flex items-center justify-center p-4 sm:p-8 bg-black/70 backdrop-blur-xl" @click="closeCard">
        <Transition name="modal-pop" appear>
          <div :key="activeCard.id" class="relative w-full max-w-5xl max-h-[90vh] rounded-3xl overflow-hidden bg-gray-900 border border-white/10 shadow-2xl shadow-black/60" @click.stop>
            <video class="w-full max-h-[55vh] object-cover" autoplay loop muted playsinline :src="activeCard.video"></video>
            <div class="p-6 sm:p-8">
              <h2 class="text-3xl sm:text-4xl font-bold tracking-tight">{{ activeCard.title }}</h2>
              <p class="text-xs sm:text-sm text-slate-400 mt-1">{{ activeCard.subtitle }}</p>
              <p class="mt-4 text-sm sm:text-base text-slate-200 leading-relaxed max-w-3xl">{{ activeCard.text }}</p>
            </div>
          </div>
        </Transition>
      </div>
    </Transition>

    <!-- ====================== 机器人档案 3D 翻转模态 ====================== -->
    <div v-if="isRobotModalOpen"
      class="robot-modal-overlay fixed inset-0 z-[60] flex items-center justify-center p-4 sm:p-8 bg-black/80 backdrop-blur-xl"
      @click="closeRobotModal">
        <div class="robot-flip-card border-2 border-gray-400/60 rounded-2xl shadow-[0_0_60px_rgba(59,130,246,0.2)]"
          :class="selectedRobot?.video ? 'w-[36rem] h-[28rem] md:w-[44rem] md:h-[30rem]' : 'w-96 h-[30rem] md:w-[28rem] md:h-[34rem]'"
          style="perspective: 1200px;"
          @click.stop="isFlipped = !isFlipped">
          <!-- 翻转内壳 -->
          <div class="relative w-full h-full transition-transform duration-700 ease-[cubic-bezier(0.4,0,0.2,1)]"
            style="transform-style: preserve-3d;"
            :style="{ transform: isFlipped ? 'rotateY(180deg)' : 'rotateY(0deg)' }">

            <!-- ===== 正面：图片/视频 ===== -->
            <div class="absolute inset-0 rounded-2xl overflow-hidden" style="backface-visibility: hidden;">
              <video v-if="selectedRobot?.video" class="w-full h-full object-cover" autoplay loop muted playsinline :src="selectedRobot.video"></video>
              <img v-else-if="selectedRobot?.image" class="w-full h-full object-cover" :src="selectedRobot.image" />
              <div v-else class="w-full h-full bg-gradient-to-br from-blue-900 via-gray-800 to-gray-900 flex items-center justify-center">
                <span class="text-5xl font-black text-gray-600 tracking-widest">{{ selectedRobot?.name }}</span>
              </div>
              <div class="absolute inset-0 bg-gradient-to-t from-black/90 via-black/20 to-transparent rounded-2xl
                          flex flex-col items-center justify-end pb-8 px-6">
                <p class="text-3xl md:text-4xl font-black tracking-wider text-white text-center drop-shadow-lg">{{ selectedRobot?.name }}</p>
              </div>
            </div>

            <!-- ===== 背面：档案信息 ===== -->
            <div class="absolute inset-0 rounded-2xl overflow-hidden bg-gray-900 border border-gray-700 shadow-2xl
                        flex flex-col justify-center p-8 md:p-10"
              style="backface-visibility: hidden; transform: rotateY(180deg);">
              <span class="text-sm text-blue-400 border border-blue-500/30 px-3 py-1 rounded-full self-start mb-5 tracking-wider">
                [ {{ selectedRobot?.type }} ]
              </span>
              <h3 class="text-3xl md:text-4xl font-extrabold text-white mb-4 tracking-tight"
                style="text-shadow: 0 0 24px rgba(59,130,246,0.4);">
                {{ selectedRobot?.name }}
              </h3>
              <div class="h-px w-20 bg-gradient-to-r from-blue-500 to-transparent mb-5"></div>
              <p class="text-base text-gray-300 leading-relaxed overflow-y-auto max-h-72">{{ selectedRobot?.description }}</p>
            </div>
          </div>
        </div>
    </div>
  </div>
</template>
