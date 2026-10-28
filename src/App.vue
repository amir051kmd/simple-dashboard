<script setup lang="ts">
import { computed, ref } from 'vue'

interface Order {
  id: number
  customer: string
  product: string
  amount: number
  status: 'موفق' | 'در انتظار' | 'لغو شده'
}

const sidebarOpen = ref(false)
const search = ref('')
const selectedMenu = ref('داشبورد')

const menuItems = ['داشبورد', 'سفارش‌ها', 'محصولات', 'کاربران', 'تنظیمات']

const orders = ref<Order[]>([
  { id: 1024, customer: 'علی رضایی', product: 'دوره Vue.js', amount: 850000, status: 'موفق' },
  { id: 1023, customer: 'محمد احمدی', product: 'دوره JavaScript', amount: 620000, status: 'در انتظار' },
  { id: 1022, customer: 'سارا کریمی', product: 'قالب فروشگاهی', amount: 410000, status: 'موفق' },
  { id: 1021, customer: 'رضا محمدی', product: 'دوره TypeScript', amount: 730000, status: 'لغو شده' },
  { id: 1020, customer: 'نگار حسینی', product: 'دوره React', amount: 900000, status: 'موفق' }
])

const filteredOrders = computed(() => {
  const value = search.value.trim().toLowerCase()

  if (!value) return orders.value

  return orders.value.filter((order) =>
    order.customer.toLowerCase().includes(value) ||
    order.product.toLowerCase().includes(value) ||
    String(order.id).includes(value)
  )
})

const totalSales = computed(() => {
  return orders.value
    .filter((order) => order.status === 'موفق')
    .reduce((sum, order) => sum + order.amount, 0)
})
//برای نمایش عدد به فرمت فارسی استفاده شده
function formatPrice(price: number) {
  return `${price.toLocaleString('fa-IR')} تومان`
}

function selectMenu(item: string) {
  selectedMenu.value = item
  sidebarOpen.value = false
}

//افزودن سفارش
function addOrder() {
  const newOrder: Order = {
    id: 1025,
    customer: 'کاربر جدید',
    product: 'دوره Vue.js',
    amount: 550000,
    status: 'موفق'
  }

  orders.value.unshift(newOrder)
}
</script>





<template>
  <div class="app">
    <aside class="sidebar" :class="{ open: sidebarOpen }">
      <div class="logo">
        <div class="logo-icon">A</div>
        <div>
          <strong>پنل مدیریت</strong>
          
        </div>
      </div>

      <div class="menu-title">منوی اصلی</div>

      <nav>
        <button
          v-for="item in menuItems"
          :key="item"
          class="menu-item"
          :class="{ active: selectedMenu === item }"
          @click="selectMenu(item)"
        >
          <span class="menu-icon">
            {{ item === 'داشبورد' ? '⌂' : item === 'سفارش‌ها' ? '▤' : item === 'محصولات' ? '▦' : item === 'کاربران' ? '◉' : '⚙' }}
          </span>
          {{ item }}
        </button>
      </nav>

      <div class="sidebar-bottom">
        <div class="user-mini">
          <div class="avatar">ا</div>
          <div>
            <strong>امیر محمد</strong>
            <span>مدیر سایت</span>
          </div>
        </div>
      </div>
    </aside>

    <div v-if="sidebarOpen" class="overlay" @click="sidebarOpen = false"></div>

    <main class="main">
      <header class="topbar">
        <button class="mobile-menu" @click="sidebarOpen = !sidebarOpen">☰</button>

        <div>
          <h1>سلام امیر </h1>
          <p>به داشبورد مدیریت خوش آمدی.</p>
        </div>

        <div class="topbar-actions">
          <button class="notification">🔔<span></span></button>
          <div class="date-box">امروز، ۲ فروردین ۱۳۳۳</div>
        </div>
      </header>

      <section class="stats">
        <div class="stat-card">
          <div class="stat-icon purple">💰</div>
          <div>
            <span>فروش این ماه</span>
            <strong>{{ formatPrice(totalSales) }}</strong>
            <small class="up">↑ ۱۲٪ نسبت به ماه قبل</small>
          </div>
        </div>

        <div class="stat-card">
          <div class="stat-icon blue">🛒</div>
          <div>
            <span>سفارش‌ها</span>
            <strong>۱۲۴</strong>
            <small>۸ سفارش جدید</small>
          </div>
        </div>

        <div class="stat-card">
          <div class="stat-icon green">👥</div>
          <div>
            <span>کاربران</span>
            <strong>۲,۴۸۰</strong>
            <small>۱۴۵ کاربر جدید</small>
          </div>
        </div>

        <div class="stat-card">
          <div class="stat-icon orange">📦</div>
          <div>
            <span>محصولات</span>
            <strong>۳۲</strong>
            <small>۴ محصول جدید</small>
          </div>
        </div>
      </section>

      <section class="content-grid">
        <div class="card sales-card">
          <div class="card-header">
            <div>
              <h2>آمار فروش</h2>
              <p>فروش ۷ روز اخیر</p>
            </div>
            <select>
              <option>این هفته</option>
              <option>این ماه</option>
            </select>
          </div>

          <div class="simple-chart">
            <div class="chart-lines">
              <span></span><span></span><span></span><span></span>
            </div>
            <div class="bars">
              <div v-for="(height, index) in [45, 70, 55, 85, 62, 94, 76]" :key="index" class="bar-wrap">
                <div class="bar" :style="{ height: `${height}%` }"></div>
                <small>{{ ['ش', 'ی', 'د', 'س', 'چ', 'پ', 'ج'][index] }}</small>
              </div>
            </div>
          </div>
        </div>

        <div class="card quick-card">
          <div class="card-header">
            <div>
              <h2>عملیات سریع</h2>
              <p>کارهای پرکاربرد</p>
            </div>
          </div>

          <div class="quick-actions">
            <button @click="addOrder">
              <span>＋</span>
              <div>
                <strong>افزودن سفارش</strong>
                <small>یک سفارش آزمایشی اضافه کن</small>
              </div>
            </button>
            <button>
              <span>＋</span>
              <div>
                <strong>افزودن محصول</strong>
                <small>محصول جدید ثبت کن</small>
              </div>
            </button>
            <button>
              <span>👤</span>
              <div>
                <strong>کاربرها</strong>
                <small>مشاهده کاربران سایت</small>
              </div>
            </button>
          </div>
        </div>
      </section>

      <section class="card orders-card">
        <div class="card-header orders-header">
          <div>
            <h2>آخرین سفارش‌ها</h2>
            <p>لیست آخرین سفارش‌های ثبت‌شده</p>
          </div>

          <div class="header-tools">
            <input v-model="search" type="text" placeholder="جستجو در سفارش‌ها..." />
            <button class="see-all">مشاهده همه</button>
          </div>
        </div>

        <div class="table-wrap">
          <table>
            <thead>
              <tr>
                <th>شماره</th>
                <th>مشتری</th>
                <th>محصول</th>
                <th>مبلغ</th>
                <th>وضعیت</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="order in filteredOrders" :key="order.id">
                <td>#{{ order.id }}</td>
                <td>{{ order.customer }}</td>
                <td>{{ order.product }}</td>
                <td>{{ formatPrice(order.amount) }}</td>
                <td>
                  <span class="status" :class="{
                    success: order.status === 'موفق',
                    waiting: order.status === 'در انتظار',
                    danger: order.status === 'لغو شده'
                  }">
                    {{ order.status }}
                  </span>
                </td>
              </tr>
              <tr v-if="filteredOrders.length === 0">
                <td colspan="5" class="empty">موردی پیدا نشد.</td>
              </tr>
            </tbody>
          </table>
        </div>
      </section>
    </main>
  </div>
</template>
