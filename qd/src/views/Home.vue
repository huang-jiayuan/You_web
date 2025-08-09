<template>
  <div class="home-page">
    <!-- 页面头部 -->
    <Header 
      title="欢迎使用Vue3前端应用" 
      subtitle="现代化的Vue3应用模板，展示组件化架构、响应式数据和交互功能"
    />

    <!-- 功能演示区域 -->
    <section class="demo-section">
      <div class="demo-grid">
        <!-- 基础计数器演示 -->
        <Card title="基础计数器" :shadow="true">
          <template #default>
            <p class="card-description">演示Vue3的响应式数据绑定和Composition API</p>
            <Counter 
              label="点击按钮改变数值"
              :initial-value="0"
              :show-info="true"
              :show-double="true"
              @update="handleCounterUpdate"
            />
          </template>
        </Card>

        <!-- 带限制的计数器 -->
        <Card title="带限制的计数器" :shadow="true">
          <template #default>
            <p class="card-description">演示带最小值和最大值限制的计数器</p>
            <Counter 
              label="范围: 0-10"
              :initial-value="5"
              :min="0"
              :max="10"
              :bounded="true"
              :show-step-controls="true"
              :show-quick-actions="false"
            />
          </template>
        </Card>

        <!-- 主题切换演示 -->
        <Card title="主题管理" :shadow="true">
          <template #default>
            <p class="card-description">
              当前主题: <strong class="theme-indicator">{{ themeDisplayName }}</strong>
            </p>
            <div class="theme-controls">
              <Button 
                type="primary" 
                size="medium"
                @click="toggleTheme"
              >
                {{ themeButtonText }}
              </Button>
              <div class="theme-info">
                <span class="theme-status">{{ themeStatus }}</span>
              </div>
            </div>
          </template>
        </Card>

        <!-- Vue3特性展示 -->
        <Card title="Vue3 核心特性" :shadow="true">
          <template #default>
            <p class="card-description">本应用展示的Vue3现代开发特性</p>
            <div class="feature-grid">
              <div class="feature-item">
                <span class="feature-icon">⚡</span>
                <div class="feature-content">
                  <h4>Composition API</h4>
                  <p>更好的逻辑复用和代码组织</p>
                </div>
              </div>
              <div class="feature-item">
                <span class="feature-icon">🔄</span>
                <div class="feature-content">
                  <h4>响应式系统</h4>
                  <p>高效的数据绑定和更新</p>
                </div>
              </div>
              <div class="feature-item">
                <span class="feature-icon">🧩</span>
                <div class="feature-content">
                  <h4>组件化架构</h4>
                  <p>可重用的UI组件设计</p>
                </div>
              </div>
              <div class="feature-item">
                <span class="feature-icon">🎨</span>
                <div class="feature-content">
                  <h4>主题系统</h4>
                  <p>支持明暗主题切换</p>
                </div>
              </div>
            </div>
          </template>
          <template #actions>
            <Button type="secondary" size="small" @click="showMoreFeatures">
              了解更多
            </Button>
          </template>
        </Card>

        <!-- 交互统计 -->
        <Card title="交互统计" :shadow="true">
          <template #default>
            <p class="card-description">记录用户在当前会话中的交互次数</p>
            <div class="stats-grid">
              <div class="stat-item">
                <span class="stat-number">{{ stats.counterClicks }}</span>
                <span class="stat-label">计数器点击</span>
              </div>
              <div class="stat-item">
                <span class="stat-number">{{ stats.themeToggles }}</span>
                <span class="stat-label">主题切换</span>
              </div>
              <div class="stat-item">
                <span class="stat-number">{{ stats.pageViews }}</span>
                <span class="stat-label">页面访问</span>
              </div>
            </div>
          </template>
          <template #actions>
            <Button type="danger" size="small" @click="resetStats">
              重置统计
            </Button>
          </template>
        </Card>
      </div>
    </section>
  </div>
</template>

<script>
import { ref, computed, onMounted, watch } from 'vue'
import { useTheme } from '../composables/useTheme.js'
import Header from '../components/common/Header.vue'
import Card from '../components/ui/Card.vue'
import Counter from '../components/ui/Counter.vue'
import Button from '../components/ui/Button.vue'

export default {
  name: 'Home',
  components: {
    Header,
    Card,
    Counter,
    Button
  },
  setup() {
    // 使用主题管理
    const { theme, isDark, toggleTheme, initTheme } = useTheme()

    // 交互统计
    const stats = ref({
      counterClicks: 0,
      themeToggles: 0,
      pageViews: 1
    })

    // 计算属性
    const themeDisplayName = computed(() => {
      return isDark.value ? '暗色主题' : '明亮主题'
    })

    const themeButtonText = computed(() => {
      return `切换到${isDark.value ? '明亮' : '暗色'}主题`
    })

    const themeStatus = computed(() => {
      return isDark.value ? '🌙 夜间模式已启用' : '☀️ 日间模式已启用'
    })

    // 方法
    const handleCounterUpdate = (value) => {
      stats.value.counterClicks++
    }

    const handleThemeToggle = () => {
      toggleTheme()
      stats.value.themeToggles++
    }

    const showMoreFeatures = () => {
      // 这里可以导航到关于页面或显示更多信息
      alert('更多特性请查看关于页面！')
    }

    const resetStats = () => {
      stats.value = {
        counterClicks: 0,
        themeToggles: 0,
        pageViews: stats.value.pageViews
      }
    }

    // 生命周期
    onMounted(() => {
      initTheme()
      
      // 从localStorage恢复统计数据
      const savedStats = localStorage.getItem('home-stats')
      if (savedStats) {
        try {
          const parsed = JSON.parse(savedStats)
          stats.value = {
            ...parsed,
            pageViews: parsed.pageViews + 1
          }
        } catch (error) {
          console.warn('无法恢复统计数据:', error)
        }
      }

      // 监听统计变化并保存
      const saveStats = () => {
        localStorage.setItem('home-stats', JSON.stringify(stats.value))
      }

      // 使用watch监听stats变化
      watch(stats, saveStats, { deep: true })
    })

    return {
      // 主题相关
      theme,
      isDark,
      themeDisplayName,
      themeButtonText,
      themeStatus,
      toggleTheme: handleThemeToggle,
      
      // 统计数据
      stats,
      
      // 方法
      handleCounterUpdate,
      showMoreFeatures,
      resetStats
    }
  }
}
</script>

<style scoped>
.home-page {
  min-height: 100vh;
}

/* 演示区域 */
.demo-section {
  padding: var(--spacing-2xl) 0;
}

.demo-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(350px, 1fr));
  gap: var(--spacing-xl);
}

/* 卡片内容样式 */
.card-description {
  color: var(--color-text-secondary);
  margin-bottom: var(--spacing-lg);
  line-height: 1.6;
  font-size: var(--font-size-sm);
}

/* 主题控制样式 */
.theme-controls {
  display: flex;
  flex-direction: column;
  gap: var(--spacing-md);
  align-items: center;
}

.theme-indicator {
  color: var(--color-primary);
  font-weight: 600;
}

.theme-info {
  text-align: center;
}

.theme-status {
  font-size: var(--font-size-sm);
  color: var(--color-text-muted);
  padding: var(--spacing-xs) var(--spacing-sm);
  background-color: var(--color-bg-tertiary);
  border-radius: var(--border-radius-sm);
}

/* 特性网格样式 */
.feature-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
  gap: var(--spacing-md);
  margin-top: var(--spacing-md);
}

.feature-item {
  display: flex;
  align-items: flex-start;
  gap: var(--spacing-sm);
  padding: var(--spacing-sm);
  border-radius: var(--border-radius-md);
  transition: background-color var(--transition-fast);
}

.feature-item:hover {
  background-color: var(--color-bg-tertiary);
}

.feature-icon {
  font-size: var(--font-size-xl);
  flex-shrink: 0;
  margin-top: var(--spacing-xs);
}

.feature-content h4 {
  margin: 0 0 var(--spacing-xs) 0;
  font-size: var(--font-size-sm);
  font-weight: 600;
  color: var(--color-text-primary);
}

.feature-content p {
  margin: 0;
  font-size: var(--font-size-xs);
  color: var(--color-text-secondary);
  line-height: 1.4;
}

/* 统计网格样式 */
.stats-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: var(--spacing-md);
  margin-top: var(--spacing-md);
}

.stat-item {
  text-align: center;
  padding: var(--spacing-md);
  background-color: var(--color-bg-tertiary);
  border-radius: var(--border-radius-md);
  transition: all var(--transition-fast);
}

.stat-item:hover {
  transform: translateY(-2px);
  box-shadow: var(--shadow-sm);
}

.stat-number {
  display: block;
  font-size: var(--font-size-2xl);
  font-weight: 700;
  color: var(--color-primary);
  margin-bottom: var(--spacing-xs);
}

.stat-label {
  font-size: var(--font-size-xs);
  color: var(--color-text-secondary);
  font-weight: 500;
}

/* 响应式设计 */
@media (max-width: 768px) {
  .demo-grid {
    grid-template-columns: 1fr;
    gap: var(--spacing-lg);
  }
  
  .feature-grid {
    grid-template-columns: 1fr;
  }
  
  .stats-grid {
    grid-template-columns: 1fr;
    gap: var(--spacing-sm);
  }
  
  .feature-item {
    padding: var(--spacing-xs);
  }
}

@media (max-width: 480px) {
  .demo-section {
    padding: var(--spacing-xl) 0;
  }
  
  .theme-controls {
    gap: var(--spacing-sm);
  }
  
  .stat-item {
    padding: var(--spacing-sm);
  }
  
  .stat-number {
    font-size: var(--font-size-xl);
  }
}
</style>