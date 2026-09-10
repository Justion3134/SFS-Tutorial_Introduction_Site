<script setup>
import { computed, onBeforeUnmount, onMounted, ref } from 'vue'
const isMenuOpen = ref(false), activeSection = ref('首页'); let syncNavigation
const navItems = [['首页', '#home'], ['系统成果', '#achievement'], ['核心功能', '#features'], ['系统示例', '#system'], ['项目团队', '#team']]
const features = [
  ['01', '地图导览', '景区范围、景点点位、游客位置与推荐路线汇聚于一张实时地图。', 'LBS · GIS'], ['02', '个性化讲解', '围绕儿童、学生、历史、科普与摄影五种兴趣模式，调整同一景点的叙事方式。', '5 种导览模式'], ['03', 'AI 导游问答', '先检索上方山本地知识，再结合当前点位与路线生成可信回答。', '轻量化 RAG'], ['04', '超时空角色对话', '让古树、岩溶、古钟、山风与水滴成为可被追问的景区讲述者。', '沉浸式叙事'], ['05', '多模态导游', '通过拍照、语音与实时画面，在现场识别景物并展开讲解。', '视觉 · 语音 · RTC'], ['06', '路线规划', '结合步道网络与游览节点，为不同登山节奏提供可见、可达的路线参考。', '路径网络'], ['07', '探索与成就', '探索度、健康登山、路线计时和本机排行榜，让每一步都留下可见的记录。', '本地持久化']]
const systemShots = [['地图导览', '景区范围、景点点位、游客位置与推荐路线汇聚在一张地图。', '实时位置 · 景点识别 · 路线参考'], ['景点讲解', '进入点位后，系统根据位置与游客模式生成现场讲解。', '自动识别 · 个性化内容'], ['AI 问答', '围绕当前景点追问历史、生态、路线和拍照建议。', '本地知识 · 可信回答'], ['角色对话', '让古树、岩溶、古钟、山风与水滴成为景区讲述者。', '沉浸叙事 · 超时空对话'], ['路线规划', '根据步道网络和游览节点，规划适合当下节奏的路线。', '路径参考 · 探索进度'], ['我的记录', '保存探索点位、健康登山记录和个人游览成果。', '本地记录 · 成就反馈']]
const activeSystemIndex = ref(0)
const activeSystemShot = computed(() => systemShots[activeSystemIndex.value])
const systemThumbnailStrip = ref(null)
const members = Array.from({ length: 6 }, (_, index) => String(index + 1).padStart(2, '0'))
function goTo(id) { isMenuOpen.value = false; document.querySelector(id)?.scrollIntoView({ behavior: 'smooth', block: 'start' }) }
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
      <div class="section-kicker reveal">01 / PROJECT OUTCOME</div>
      <div class="statement-layout">
        <h2 class="display-title reveal">一座山的<br><em>数字讲述者。</em></h2>
        <div class="statement-copy reveal">
          <p>上方山同时拥有森林生态、千年寺庙与岩溶洞穴。面对分散的景点、复杂的山路与不同游客的期待，我们将一套移动端智能导游带到现场。</p>
          <p>这不是一张静态地图，而是一个能够定位、理解、讲述并陪伴探索的超时空对话导游系统。</p>
          <dl>
            <div>
              <dt>14</dt>
              <dd>景区点位</dd>
            </div>
            <div>
              <dt>45</dt>
              <dd>知识片段</dd>
            </div>
            <div>
              <dt>05</dt>
              <dd>对话角色</dd>
            </div>
            <div>
              <dt>06</dt>
              <dd>成员共创</dd>
            </div>
          </dl>
        </div>
      </div>
      <div class="terrain-silhouette"><span></span><span></span><span></span></div>
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
    </section>
    <section id="features" class="features" data-nav="核心功能">
      <header class="section-heading reveal">
        <p class="section-kicker">02 / SYSTEM CAPABILITIES</p>
        <h2>从脚下的路，<br>到眼前的故事。</h2>
        <p>一条从“定位游客”到“持续探索”的现场导览闭环。</p>
      </header>
      <div class="feature-list">
        <article v-for="f in features" :key="f[0]" class="feature-item reveal"><span class="feature-no">{{ f[0] }}</span>
          <div>
            <h3>{{ f[1] }}</h3>
            <p>{{ f[2] }}</p>
          </div><span class="feature-tag">{{ f[3] }}</span><span class="feature-arrow">↗</span>
        </article>
      </div>
    </section>
    <section id="system" class="system-showcase section-dark" data-nav="系统示例">
      <header class="section-heading reveal">
        <p class="section-kicker">03 / SYSTEM PREVIEW</p>
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
            <div class="phone-placeholder"><span>截图预留位置</span><small>{{ activeSystemShot[0] }} 页面截图</small></div>
            <div class="phone-label">{{ activeSystemShot[0] }} / SYSTEM PREVIEW</div>
          </div>
          <div class="system-orbit orbit-a"></div>
          <div class="system-orbit orbit-b"></div><span class="stage-point p-one"></span><span
            class="stage-point p-two"></span>
        </div>
      </div>
    </section>
    <section class="flow">
      <p class="section-kicker reveal">04 / HOW IT WORKS</p>
      <h2 class="reveal">在山里，<em>每一步都有回应。</em></h2>
      <div class="flow-path reveal"><template
          v-for="(x, i) in [['01', '定位', '识别游客所在位置'], ['02', '检索', '关联景区本地资料'], ['03', '生成', '组织个性化讲解'], ['04', '探索', '记录行走与发现']]"
          :key="x[0]">
          <div><b>{{ x[0] }}</b><i>{{ x[1] }}</i><small>{{ x[2] }}</small></div><span v-if="i < 3"></span>
        </template>
      </div>
    </section>
    <section id="team" class="team section-dark" data-nav="项目团队">
      <header class="team-heading reveal">
        <div>
          <p class="section-kicker">05 / TEAM FOUR</p>
          <h2>六个人，<br>一座山。</h2>
        </div>
        <p>中国地质大学（北京）<br>上方山国家森林公园实习 · 第 4 组</p>
      </header>
      <div class="team-group-photo reveal">
        <div><span>GROUP 04 / SHANGFANGSHAN</span><strong>六人合照预留位置</strong>
          <p>建议使用横向团队合照</p>
        </div><b>04</b>
      </div>
      <div class="member-grid">
        <article v-for="number in members" :key="number" class="member-card reveal">
          <div class="member-portrait"><span>成员照片<br>预留位置</span><b>{{ number }}</b></div>
          <div class="member-info"><span>TEAM MEMBER / {{ number }}</span>
            <h3>成员姓名</h3>
            <p>个人职责 · 待补充</p><i>“个人宣传标语预留位置”</i>
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
