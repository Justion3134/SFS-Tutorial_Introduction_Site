<script setup>
import { computed, onBeforeUnmount, onMounted, ref } from 'vue'
const isMenuOpen = ref(false), activeSection = ref('首页'); let syncNavigation
const navItems = [['首页', '#home'], ['系统成果', '#achievement'], ['核心技术', '#features'], ['功能展示', '#system'], ['项目团队', '#team']]
const features = [
  ['01', '实时地图导览', '基于 Leaflet 地图引擎，融合高德 POI 数据与本地步道路网，实时呈现景点位置与推荐路线。', 'Leaflet · POI · GIS'],
  ['02', '后台持续定位', '原生前台定位服务，游客离开 App 也能持续记录轨迹与探索进度。', 'Capacitor · GPS'],
  ['03', '轻量化 RAG 问答', '本地知识库检索 + 大模型问答，先查景区资料再生成回答，减少 AI 幻觉。', 'Fuse.js · LLM'],
  ['04', '超时空角色对话', '古树、岩溶、古钟等五种人设，基于场景化提示词工程，让文物与自然“开口说话”。', 'Prompt · Personas'],
  ['05', '实时语音对话', '接入火山引擎 RTC 与语音大模型，支持自然语音交流与语音合成讲解。', 'RTC · TTS'],
  ['06', '多模态识别', '拍照或取景即可识别景物并生成讲解，支持图像与文本的联合理解。', 'Vision · Image'],
  ['07', '本地数据持久化', '基于 IndexedDB 的本地数据库，离线也能保存探索记录、路线与个人成就。', 'IndexedDB · Offline']]
const systemShots = [
  ['地图导览', '景区范围、景点点位、游客当前位置与推荐路线汇聚在一张地图。\n跟随清晰的空间线索，从山脚一路找到下一处值得停留的故事。', '实时位置 · 景点识别 · 路线参考'],
  ['个性化讲解', '围绕儿童、学生、历史、科普与摄影等不同兴趣，为同一景点调整讲述方式。\n走近一处风景，就获得一段更贴合当下视角的现场解读。', '自动识别 · 个性化内容'],
  ['AI 导游问答', '围绕当前景点继续追问历史、生态、路线和拍照建议，回答紧扣现场情境。\n先关联上方山本地资料，再把你真正关心的问题讲清楚。', '本地知识 · 可信回答'],
  ['超时空角色对话', '古树、岩溶、古钟、山风与水滴化身为可被追问的景区讲述者。\n让自然与遗迹用各自的口吻，带你进入一段跨越时间的对话。', '沉浸叙事 · 超时空对话'],
  ['多模态导游', '拍下一株植物、一块岩石或眼前的古建，便可发起图像与文字联合讲解。\n也可以通过语音和实时画面，把问题直接带到山中现场。', '视觉 · 语音 · RTC'],
  ['路线规划', '结合步道网络、游览节点与当前进度，为不同登山节奏提供路线参考。\n提前看见下一段路的方向，把体力和时间留给真正想看的风景。', '路径参考 · 探索进度'],
  ['探索与成就', '每到一处点位即可点亮探索进度，解锁属于你的上方山足迹。\n健康登山计时、个人路线记录与本机榜单，让每一步都像完成一项山野挑战。', '点亮地图 · 登山挑战 · 成就榜单']]
const techStack = ['Vue 3', 'Leaflet 地图', '轻量化 RAG', '大模型对话', '实时语音 RTC', '多模态视觉', '本地持久化']
const activeSystemIndex = ref(0)
const activeSystemShot = computed(() => systemShots[activeSystemIndex.value])
const systemThumbnailStrip = ref(null)
const teamMembers = [
  { number: '01', name: '关威', role: '组长', motto: 'Reconnecting... waiting for network', image: '/introduction/1/e8620990-16ef-4e15-afcc-07ebe3316ff8.png' },
  { number: '02', name: '林一男', role: '副组长', motto: '가는 정이 있어야 오는 정이 있다', image: '/introduction/2/d4697e56-ceef-45bb-82be-02dd70f21e7b.png' },
  { number: '03', name: '贾士轩', role: '数据采集与系统测试', motto: '这gpt怎么又宕机了！?', image: '/introduction/3/c86072c5-5963-4121-a4de-eadfce47f04e.png' },
  { number: '04', name: '马靖宇', role: '核心开发', motto: 'ᯤ 正在重新连接 5∕5', image: '/introduction/4/04e5b128-d1fc-4c7c-8bf1-e96235958105.png' },
  { number: '05', name: '邱源桃', role: '素材收集和功能调研', motto: '孤独的吗喽［(－－)］zzz', image: '/introduction/5/1013627c-0633-4307-a925-831fbd380e0a.png' },
  { number: '06', name: '李泽华', role: '后勤保障和数据处理', motto: '人不能一直活着', image: '/introduction/6/a26b1396-5fde-4a26-9aa7-f3b191741902.png' }
]
function goTo(id) { isMenuOpen.value = false; document.querySelector(id)?.scrollIntoView({ behavior: 'smooth', block: 'start' }) }
function openGuideSystem() {
  window.location.href = 'http://localhost:5174'
}
function selectSystem(index) {
  const nextIndex = Math.max(0, Math.min(systemShots.length - 1, index))
  activeSystemIndex.value = nextIndex
  window.requestAnimationFrame(() => {
    systemThumbnailStrip.value?.children[nextIndex]?.scrollIntoView({ behavior: 'smooth', block: 'nearest', inline: 'center' })
  })
}
onMounted(() => {
  const sections = [...document.querySelectorAll('[data-nav]')]
  const updateActiveSection = () => {
    const marker = window.scrollY + 120
    let current = sections[0]?.dataset.nav || '首页'
    sections.forEach((section) => {
      if (section.offsetTop <= marker) current = section.dataset.nav || current
    })
    activeSection.value = current
  }
  syncNavigation = () => window.requestAnimationFrame(updateActiveSection)
  window.addEventListener('scroll', syncNavigation, { passive: true })
  window.addEventListener('resize', syncNavigation)
  updateActiveSection()
})
onBeforeUnmount(() => {
  if (!syncNavigation) return
  window.removeEventListener('scroll', syncNavigation)
  window.removeEventListener('resize', syncNavigation)
})
</script>

<template>
  <main>
    <header class="site-header" :class="{ 'menu-open': isMenuOpen }">
      <a class="brand" href="#home" @click.prevent="goTo('#home')"><img class="brand-mark"
          src="/assets/site/group4-logo.png"
          alt="中国地质大学（北京）第4组上方山超时空对话导游系统标志"><span><b>上方山</b><small>超时空对话导游系统</small></span></a>
      <nav class="desktop-nav"><button v-for="[label, id] in navItems" :key="label"
          :class="{ active: activeSection === label }" @click="goTo(id)">{{ label }}</button></nav>
      <button class="try-now-button" type="button" @click="openGuideSystem">Try Now</button>
      <button class="menu-button" aria-label="打开导航" @click="isMenuOpen = !isMenuOpen"><i></i><i></i></button>
      <nav class="mobile-nav"><button v-for="[label, id] in navItems" :key="label" @click="goTo(id)">{{ label }}</button>
      </nav>
    </header>
    <section id="home" class="hero" data-nav="首页">
      <div class="hero-photo"></div>
      <div class="hero-grid"></div>
      <div class="hero-vignette"></div>
      <div class="hero-copy reveal">
        <p class="eyebrow"><span></span> 中国地质大学（北京）· 3S综合实习 · 第 4 组</p>
        <p class="system-label">SHANGFANGSHAN TIME-SPACE DIALOGUE GUIDE SYSTEM</p>
        <h1>上方山<em>超时空对话导游系统</em></h1>
        <!-- <p class="hero-intro">让山，开口说话<br>用定位、知识库与 AI 对话，把景区导览带到游客身边。</p> -->
        <button
          class="text-action" @click="goTo('#achievement')"><span>探索系统成果</span><b>↓</b></button>
      </div>
      <div class="hero-coordinates">39° 40′ N&nbsp;&nbsp;&nbsp;115° 49′ E</div>
      <div class="hero-caption"><span>SHANGFANGSHAN NATIONAL FOREST PARK</span><span>FIELD PRACTICE · 2026</span></div>
    </section>
    <section id="achievement" class="statement section-dark" data-nav="系统成果">
      <div class="orbital-line"></div>
      <div class="section-kicker reveal">01 / 系统成果</div>
      <div class="statement-layout">
        <h2 class="display-title reveal"><span>穿越山水，</span><em>聆听千年。</em><small>上方山超时空对话导游系统</small></h2>
        <div class="statement-copy reveal">
          <p class="statement-lead">上方山，坐落于北京西南，山林苍翠、峰峦叠秀，拥有丰富的自然景观与深厚的人文底蕴。这里既有独特的山地地貌、森林生态与丰富的生物资源，也承载着悠久的历史文化、古刹遗迹与民间传说，是集自然观光、生态体验、历史文化与科普教育于一体的综合性旅游胜地。</p>
          <p>然而，一座山的故事，远不止眼前所见。</p>
          <p>我们以 3S 技术与人工智能为核心，构建“上方山超时空对话导游系统”，将山水、历史与科技融入一张可交互的数字地图。游客不仅能够看见上方山，更可以沿着空间轨迹探索景点，了解自然资源与人文遗迹，并通过智能对话，与不同时空中的“上方山”展开交流，让静态的景观变成可以阅读、可以互动、可以探索的鲜活故事。</p>
          <p>从山川地貌到森林生态，从古刹遗迹到历史传说，从现实游览到数字导览，我们希望打破传统导游中“看景点、听介绍”的单向体验，让每一次行走都成为一次发现，让每一处景观都有自己的故事。</p>
          <p class="statement-emphasis">让科技连接空间，让对话穿越时间。</p>
          <p class="statement-closing">上方山不只是一个目的地，更是一座等待被探索的“时空博物馆”。现在，跟随超时空对话导游系统，开启一场跨越山水与历史的沉浸式探索之旅。</p>
        </div>
        <div class="landscape-band">
          <div class="landscape-panel cave">
            <div>
              <p class="eyebrow"><span></span> KARST CAVE</p>
              <h3>岩溶，记录山的时间。</h3>
              <p>云水洞深处的钟乳与岩层，是自然书写的地质档案。</p>
            </div>
          </div>
          <div class="landscape-panel temple">
            <div>
              <p class="eyebrow"><span></span> CULTURAL RELICS</p>
              <h3>古刹，守望山的记忆。</h3>
              <p>寺院、古树与山道，共同构成上方山的人文坐标。</p>
            </div>
          </div>
        </div>
      </div>
      <div class="terrain-silhouette"><span></span><span></span><span></span></div>
    </section>
  <section id="features" class="features" data-nav="核心技术">
    <header class="section-heading reveal">
      <p class="section-kicker">02 / 核心技术</p>
      <h2>关键技术，<br>让山开口说话。</h2>
      <p>从定位、地图到知识检索与实时对话，支撑每一次在山中的智能回应。</p>
      </header>
      <div class="feature-list">
        <article v-for="f in features" :key="f[0]" class="feature-item reveal"><span class="feature-no">{{ f[0] }}</span>
          <div>
            <h3>{{ f[1] }}</h3>
            <p>{{ f[2] }}</p>
          </div><span class="feature-tag">{{ f[3] }}</span><span class="feature-arrow">↗</span>
        </article>
      </div>
      <section class="flow flow-in-technologies">
        <p class="section-kicker reveal">技术流程</p>
        <h2 class="reveal">在山里，<em>每一步都有回应。</em></h2>
        <div class="flow-path reveal"><template
            v-for="(x, i) in [['01', '定位', '识别游客所在位置'], ['02', '检索', '关联景区本地资料'], ['03', '生成', '组织个性化讲解'], ['04', '探索', '记录行走与发现']]"
            :key="x[0]">
            <div><b>{{ x[0] }}</b><i>{{ x[1] }}</i><small>{{ x[2] }}</small></div><span v-if="i < 3"></span>
          </template>
        </div>
      </section>
      <!-- <div class="tech-stack reveal">
        <span class="tech-stack-label">TECH STACK</span>
        <ul class="tech-stack-list"><li v-for="t in techStack" :key="t">{{ t }}</li></ul>
      </div> -->
    </section>
    <section id="system" class="system-showcase section-dark" data-nav="功能展示">
      <header class="section-heading reveal">
        <p class="section-kicker">03 / 功能展示</p>
        <h2>把整个上方山，<br>装进口袋。</h2>
        <p>选择一个功能，查看它在导游系统中的使用方式。</p>
      </header>
      <div class="system-carousel">
        <button class="system-carousel-arrow previous" type="button" aria-label="上一个系统功能"
          :disabled="activeSystemIndex === 0" @click="selectSystem(activeSystemIndex - 1)"><span aria-hidden="true">‹</span></button>
        <div ref="systemThumbnailStrip" class="system-thumbnails" role="tablist" aria-label="系统功能示例"><button
            v-for="([title, copy], index) in systemShots" :id="`system-tab-${index}`" :key="title" class="system-thumbnail"
            :class="{ active: activeSystemIndex === index }" role="tab" :aria-selected="activeSystemIndex === index"
            :aria-controls="`system-panel-${index}`"
            @click="selectSystem(index)"><span>0{{ index + 1 }}</span><strong>{{ title }}</strong><small>{{ copy }}</small></button>
        </div>
        <button class="system-carousel-arrow next" type="button" aria-label="下一个系统功能"
          :disabled="activeSystemIndex === systemShots.length - 1" @click="selectSystem(activeSystemIndex + 1)"><span aria-hidden="true">›</span></button>
      </div>
      <div :id="`system-panel-${activeSystemIndex}`" :key="activeSystemIndex" class="system-detail" role="tabpanel"
        :aria-labelledby="`system-tab-${activeSystemIndex}`">
        <div class="system-detail-copy">
          <p class="section-kicker">FUNCTION / 0{{ activeSystemIndex + 1 }}</p>
          <h3>{{ activeSystemShot[0] }}</h3>
          <p>{{ activeSystemShot[1] }}</p><span class="detail-note">{{ activeSystemShot[2] }}</span>
        </div>
        <div class="device-stage">
          <div class="phone">
            <div class="phone-top"></div>
            <div class="phone-preview">
              <video :key="activeSystemIndex" :src="`/video/${activeSystemIndex + 1}.mp4`" autoplay muted loop playsinline
                :aria-label="`${activeSystemShot[0]} 功能演示视频`"></video>
            </div>
            <div class="phone-label">{{ activeSystemShot[0] }} / SYSTEM PREVIEW</div>
          </div>
          <div class="system-orbit orbit-a"></div>
          <div class="system-orbit orbit-b"></div><span class="stage-point p-one"></span><span
            class="stage-point p-two"></span>
        </div>
      </div>
    </section>
    <section id="team" class="team team-light" data-nav="项目团队">
      <header class="team-heading reveal">
        <div>
          <p class="section-kicker">04 / 项目团队</p>
          <h2>六个人，<br>一座山。</h2>
        </div>
        <p>中国地质大学（北京）<br>上方山国家森林公园实习 · 第 4 组</p>
      </header>
      <div class="team-group-photo reveal">
      </div>
      <div class="member-grid">
        <article v-for="member in teamMembers" :key="member.number" class="member-card reveal">
          <div class="member-portrait full-photo" :style="{ backgroundColor: '#fff' }"><img :src="member.image" :alt="`${member.name}的个人照片`" loading="lazy" :style="{ objectFit: 'contain', objectPosition: 'center', backgroundColor: '#fff', filter: 'none' }"><b>{{ member.number }}</b></div>
          <div class="member-info"><span>TEAM MEMBER / {{ member.number }}</span>
            <h3>{{ member.name }}</h3>
            <p>{{ member.role }}</p><i>“{{ member.motto }}”</i>
          </div>
        </article>
      </div>
      <div class="team-footer reveal"><img src="/assets/site/group4-logo.png" alt="第4组上方山超时空对话导游系统标志">
        <p>我们在上方山相遇，<br>也让更多人与上方山相遇。</p>
      </div>
    </section>
    <footer><span>SHANGFANGSHAN TIME-SPACE DIALOGUE GUIDE SYSTEM</span><span>© 2026 GROUP 04 · CUGB</span></footer>
  </main>
</template>
