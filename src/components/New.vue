<template>
  <div class="site-wrapper">
    <div class="main-content-area">

      <!-- ГЛАВНЫЙ ЖУРНАЛЬНЫЙ БАННЕР -->
      <header class="editorial-banner">
        <div class="editorial-grid">
          <div class="text-block">
            <div class="meta-info"> Noble Desserts </div>
            <h1 class="main-title">
              <span class="word-wrap" v-for="(word, i) in 'Безупречный подход к созданию чистого вкуса'.split(' ')" :key="i">
                <span class="anim-word" :style="{ animationDelay: i * 0.05 + 's' }">{{ word }}&nbsp;</span>
              </span>
            </h1>
            <div class="divider"></div>
            <p class="manifesto">
              Я верю, что идеальный десерт это не просто сахар и крем. Это точный расчет пропорций, геометрия слоев и честные локальные продукты. Каждое изделие создается вручную с бескомпромиссным вниманием к деталям. Как в точной механике, здесь нет места случайностям.
            </p>
            <div class="signature">Десерты в исполнении Надежды Динченко</div>
          </div>
          <div class="image-block">
            <img :src="bannerImg" alt="Безупречная структура десерта" class="cover-photo" />
          </div>
        </div>
      </header>
      
      <!-- НАВИГАЦИЯ И ФИЛЬТРЫ -->
      <nav class="action-bars">
        <button 
          class="filter-btn" 
          :class="{ active: currentCategory === 'bakery' }"
          @click="currentCategory = 'bakery'">
         Выпечка 
        </button>
        <button 
          class="filter-btn" 
          :class="{ active: currentCategory === 'desserts' }"
          @click="currentCategory = 'desserts'"
        >
         Десерты и пирожные
        </button>
      </nav>

      <!-- СЕТКА КАТАЛОГА -->
      <main class="catalog-grid">
  <TransitionGroup name="catalog-list">

    <!-- ВСТАВЛЯТЬ СТРОГО СЮДА: -->
    <div 
      v-for="product in filteredProducts" 
      :key="product.id" 
      class="product-card"
      v-scroll-reveal
    >

      <div class="slider-area">
        <div v-if="product.badge" class="card-badge">{{ product.badge }}</div>
        
        <button v-if="product.images.length > 1" class="arrow left" @click="prevImage(product)">‹</button>
        
        <img 
          :src="product.images[product.currentImgIndex]" 
          :alt="product.title" 
          class="card-img" 
          :class="{ 'img-fade': animCardId === product.id }"
          loading="lazy"
        />
        
        <button v-if="product.images.length > 1" class="arrow right" @click="nextImage(product)">›</button>
        <div v-if="product.images.length > 1" class="slider-dots">
          <span 
            v-for="(img, index) in product.images" 
            :key="index"
            class="dot"
            :class="{ active: index === product.currentImgIndex }"
          ></span>
        </div>
      </div>
      
      <div class="card-info">
        <h3 class="product-title">{{ product.title }}</h3>
        <div class="product-weight">{{ product.weight }}</div>
        <p class="product-desc">{{ product.desc }}</p>
        <div class="price-tag">{{ product.price }}</div>
      </div>

    </div> <!-- Закрывающий тег карточки -->

  </TransitionGroup>
</main>

      <!-- ПОДВАЛ -->
      <footer class="footer">
        <div class="contact-section">
          <h3>Для обсуждения заказа и индивидуального декора:</h3>
          <div class="links-box">
            <a :href="maxUrl" rel="noopener noreferrer" class="w-40 text-center link-btn">MAX</a>       
            <a :href="telegramUrl" target="_blank" rel="noopener noreferrer" class="link-btn">TELEGRAM</a>
          </div>
        </div>
        <p class="copyright">© 2026 Dinchenko. Все права защищены.</p>
      </footer>

      <button v-if="showScrollBtn" class="btn-up" @click="scrollToTop">↑</button>

    </div>
  </div>
</template>


<script setup>
import { ref, onMounted, onUnmounted, watch, nextTick, computed } from 'vue'


import bannerImg from '../assets/banner.jpg'
import eclairsImg from '../assets/eclairs.webp'
import eclairscutImg from '../../IMG_20260524_212817.webp'
import milfierImg from '../assets/IMG_20260503_153550.webp'
import cakeImg from '../assets/IMG_20260503_211046.webp'
import tartImg from '../assets/IMG153828.webp'
import tartCutImg from '../assets/IMG154557.webp'
import blackforestImg from '../assets/IMG_20260504_154535.webp'
import blackforestcutImg from '../assets/IMG_20260504_154550.webp'
import blackforestnewImg from '../assets/IMG_20260504_153943 (1).webp'
import blackforestcutnewImg from '../assets/IMG_20260504_153948.webp'
import pryanikSetImg from '../assets/Screenshot 2026-05-04 175052.jpg'
import pryanikCircleImg from '../assets/Screenshot 2026-05-04 175121.jpg'
import pryanikHouseImg from '../assets/Screenshot 2026-05-04 175155.jpg'
import pryanikBigImg from '../assets/Screenshot 2026-05-04 175208.jpg'
import pryanikCutImg from '../assets/Screenshot 2026-05-04 175138.jpg'
import zaherCutImg from '../assets/IMG_20260504_154047.webp'
import zyzhikImg from '../assets/IMG_20260504_154103.webp'
import zyzhikcutImg from '../assets/Screenshot 2026-05-12 190038.jpg'
import bombaImg from '../assets/IMG_20260504_153904.webp'
import schtollenImg from '../assets/IMG_20260504_154230.webp'
  


import zaherImg from '../../IMG_20260509_120359 (1) 1.webp'
import macaronImg from '../../14.webp'
import macaroncutImg from '../../11.webp'
const currentCategory = ref('desserts')
const animCardId = ref(null)
const showScrollBtn = ref(false)
const allProducts = ref([
  { 
    id: 1, 
    title: 'Французский «Мильфей»', 
    price: 'По запросу', 
    category: 'desserts', 
    images: [milfierImg], 
    currentImgIndex: 0,
    badge: 'Фирменный рецепт',
    weight: '12*8 см',
    desc: 'Хрустящие коржи из слоеного теста, сливочный крем «Diplomat»'
  },
  { 
    id: 2, 
    title: 'Карамельные Макарон', 
    price: 'По запросу', 
    category: 'desserts', 
    images: [macaronImg, macaroncutImg], 
    currentImgIndex: 0,
    badge: 'Новинка',
    weight: 'набор из 5 шт.',
    desc: 'Выполнен на итальянской меренге, ганаш из карамельного шоколада со сливками, мягкая соленая карамель'
  },
  { 
    id: 3, 
    title: 'Грушевая «Шарлотт»', 
    price: 'По запросу', 
    category: 'desserts', 
    images: [cakeImg], 
    currentImgIndex: 0,
    badge: 'Премиум',
    weight: 'от 1 кг',
    desc: 'Бисквит Савоярди, карамелизованные груши, крем «Bavarois»'
  },
  { 
    id: 4, 
    title: 'Эклеры «New York Cheesecake»', 
    price: 'По запросу', 
    category: 'desserts', 
    images: [eclairsImg, eclairscutImg], 
    currentImgIndex: 0,
    badge: 'Спецпредложение',
    weight: 'набор из 3 шт.',
    desc: 'Безглютеновое тесто, творожно-сливочный крем, клубничное желе'
  },
  { 
    id: 5, 
    title: 'Муссовый торт «Sachertorte»', 
    price: 'По запросу', 
    category: 'desserts', 
    images: [zaherImg, zaherCutImg], 
    currentImgIndex: 0,
    weight: 'от 1 кг',
    desc: 'Шоколадный бисквит, прослойка из абрикосового и мандаринового конфитюра в сочетании с «бобами тонка», шоколадный мусс'
  },
  { 
    id: 6, 
    title: 'Тарталетка с ягодами', 
    price: 'По запросу', 
    category: 'desserts', 
    images: [tartImg, tartCutImg], 
    currentImgIndex: 0,
    weight: '1 шт.',
    desc: 'Песочное тесто, ванильный ганаш, ягодное желе, свежие ягоды'
  },
  { 
    id: 7, 
    title: 'Имбирные пряники', 
    price: 'По запросу', 
    category: 'bakery', 
    images: [pryanikSetImg, pryanikCircleImg, pryanikHouseImg], 
    currentImgIndex: 0,
    weight: '1 шт.',
    desc: 'Ароматное медовое тесто с имбирем и корицей, ручная художественная роспись сахарной глазурью'
  },
  { 
    id: 8, 
    title: 'Печатный пряник с начинкой', 
    price: 'По запросу', 
    category: 'bakery', 
    images: [pryanikBigImg, pryanikCutImg], 
    currentImgIndex: 0,
    weight: '1 шт.',
    desc: 'Традиционное медовое тесто с пряностями, густая начинка из протертой домашней смородины и яблок'
  },
  { 
    id: 9, 
    title: 'Брауни «Irish Stout»', 
    price: 'По запросу', 
    category: 'desserts', 
    images: [bombaImg], 
    currentImgIndex: 0,
    weight: 'от 300 г',
    desc: 'Брауни на пиве Stout с грецким орехом, ванильный ганаш, карамель'  
  },
  { 
    id: 10, 
    title: 'Ванильные профитроли с шоколадным ганашем', 
    price: 'По запросу', 
    category: 'desserts', 
    images: [zyzhikImg, zyzhikcutImg], 
    currentImgIndex: 0,
    weight: 'набор из 5 шт.',
    desc: 'Сливочный крем с натуральной ванилью и насыщенный шоколадный ганаш'
  },
  { 
    id: 11, 
    title: 'Классический «Черный лес»', 
    price: 'По запросу', 
    category: 'desserts', 
    images: [blackforestImg, blackforestcutImg], 
    currentImgIndex: 0,
    weight: 'от 1 кг',
    desc: 'Шоколадный бисквит, сливочный и шоколадный ганаш, вишневое желе'
  },
  { 
    id: 12, 
    title: 'Рождественский штоллен', 
    price: 'По запросу', 
    category: 'bakery', 
    images: [schtollenImg], 
    currentImgIndex: 0,
    weight: '500 г',
    desc: 'Творожное тесто, цукаты на роме Barceló, масло Нуазетт'
  },
  { 
    id: 13, 
    title: 'Черный лес «Modern»', 
    price: 'По запросу', 
    category: 'desserts', 
    images: [blackforestnewImg, blackforestcutnewImg], 
    currentImgIndex: 0,
    weight: 'от 1 кг',
    desc: 'Шоколадный бисквит, вишневое желе, нежный сливочный мусс'
  }
])



const filteredProducts = computed(() => {
  return allProducts.value.filter(p => p.category === currentCategory.value)
})

const nextImage = (product) => {
  animCardId.value = product.id
  setTimeout(() => {
    product.currentImgIndex = (product.currentImgIndex + 1) % product.images.length
    animCardId.value = null
  }, 200)
}

const prevImage = (product) => {
  animCardId.value = product.id
  setTimeout(() => {
    product.currentImgIndex = (product.currentImgIndex - 1 + product.images.length) % product.images.length
    animCardId.value = null
  }, 200)
}

const scrollToTop = () => {
  window.scrollTo({ top: 0, behavior: 'smooth' })
}

const handleScroll = () => {
  showScrollBtn.value = window.scrollY > 400
}

onMounted(() => { window.addEventListener('scroll', handleScroll) })
onUnmounted(() => { window.removeEventListener('scroll', handleScroll) })
const telegramUrl = ref('https://t.me/Ndesserts26') 
const maxUrl = ref('https://max.ru/u/f9LHodD0cOL4iVq41cK4V4jnAVusNP_iDcj2fr8XLmeibZDGYM8iJq-Pa2k') 
const createRevealObserver = (el) => {
  const observer = new IntersectionObserver((entries) => {
    entries.forEach((entry) => {
      if (entry.isIntersecting) {
        el.classList.add('visible')
        observer.unobserve(el.target)
      }
    })
  }, { 
    threshold: 0.05, // Уменьшено до 5%, чтобы на мобильных экранах анимация не залипала
    rootMargin: "0px 0px -50px 0px" 
  })
  observer.observe(el)
  el._revealObserver = observer // Сохраняем ссылку для последующей очистки
}
const vScrollReveal = {
  mounted(el) {
    setTimeout(() => {
      createRevealObserver(el)
    }, 150)
  },
  unmounted(el) {
    if (el._revealObserver) {
      if (el instanceof Element) {
        el._revealObserver.unobserve(el)
      }
      el._revealObserver.disconnect()
    }
  }
}


</script>
<style scoped>
/* ======================================================= */
/* 1. ГЛОБАЛЬНЫЕ СТИЛИ И ТЕМА САЙТА                        */
/* ======================================================= */
:host, :root, .site-wrapper {
  background-color: #FDFBF7 !important; 
  color: #1a1a1a !important;
  color-scheme: light !important;
}

.site-wrapper { 
  width: 100%; 
  min-height: 100vh; 
  padding: 40px 20px; 
  box-sizing: border-box;
  font-family: 'Inter', sans-serif;
}

.main-content-area { 
  max-width: 1000px; 
  margin: 0 auto;
}

/* ======================================================= */
/* 2. НАВИГАЦИЯ И ФИЛЬТРЫ КАТАЛОГА                         */
/* ======================================================= */
.action-bars { 
  display: flex; 
  justify-content: center; 
  gap: 40px;
  margin-bottom: 60px; 
}

.filter-btn { 
  flex: none; 
  border: none; 
  background: none; 
  padding: 15px 5px; 
  font-size: 16px; 
  cursor: pointer; 
  color: #1a1a1a; 
  font-family: 'Playfair Display', serif; 
  position: relative; 
  bottom: -1px; 
  transition: color 0.3s ease; 
  letter-spacing: 0.5px;
}

.filter-btn::after { 
  content: ''; 
  position: absolute; 
  bottom: 0; 
  left: 0; 
  width: 100%; 
  height: 2px; 
  background-color: #1a1a1a; 
  transform: scaleX(0); 
  transition: transform 0.4s cubic-bezier(0.16, 1, 0.3, 1);
}

.filter-btn.active::after { 
  transform: scaleX(1);
}

.filter-btn.active { 
  font-weight: 500; 
  background-color: transparent;
}

/* ======================================================= */
/* 3. ЖУРНАЛЬНЫЙ БАННЕР (EDITORIAL GRID) — ДЕСКТОП         */
/* ======================================================= */
.editorial-banner { 
  width: 100%; 
  background-color: #FDFBF7; 
  border-bottom: 1px solid #1a1a1a; 
  margin-bottom: 60px;
}

.editorial-grid { 
  display: grid; 
  grid-template-columns: 1.2fr 0.8fr; 
  min-height: 550px;
}

.text-block { 
  padding: 60px 80px 60px 0; 
  display: flex; 
  flex-direction: column; 
  justify-content: space-between; 
}

.meta-info { 
  font-family: 'Inter', sans-serif; 
  font-size: 10px; 
  font-weight: bold; 
  letter-spacing: 3px; 
  color: #838091; 
}

.main-title { 
  font-family: 'Playfair Display', serif; 
  font-size: 42px; 
  font-weight: 400; 
  line-height: 1.2; 
  color: #1a1a1a; 
  margin: 30px 0;
}

.divider { 
  width: 60px; 
  height: 1px; 
  background-color: #1a1a1a; 
  margin-bottom: 30px;
}
.manifesto { 
  font-family: 'Inter', sans-serif; 
  font-size: 14px; 
  line-height: 1.9; 
  color: #333333; 
  max-width: 450px;
}

.signature { 
  font-family: 'Playfair Display', serif; 
  font-style: italic; 
  font-size: 15px; 
  color: #1a1a1a; 
  margin-top: 40px;
}

.image-block { 
  width: 100%; 
  height: 100%; 
  overflow: hidden; 
  border-left: 1px solid #1a1a1a; 
}

.cover-photo { 
  width: 100%; 
  height: 100%; 
  object-fit: cover; 
  filter: grayscale(0.2) contrast(1.05); 
}

/* Скриптовая анимация появления букв заголовка */
.word-wrap { 
  display: inline-block; 
  overflow: hidden; 
  vertical-align: bottom;
}

.anim-word { 
  display: inline-block; 
  transform: translateY(110%); 
  animation: revealWord 1.2s cubic-bezier(0.16, 1, 0.3, 1) forwards;
}

@keyframes revealWord { 
  to { transform: translateY(0); } 
}

/* ======================================================= */
/* 4. КАТАЛОГ И КАРТОЧКИ ТОВАРОВ — ДЕСКТОП                 */
/* ======================================================= */
.catalog-grid { 
  display: grid; 
  grid-template-columns: repeat(2, 1fr); 
  gap: 40px; 
  width: 100%; 
  position: relative;
}

.product-card {
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  height: 100%;
  background-color: #ffffff;
  border-radius: 10px;
  overflow: hidden;
  border: 1px solid rgba(26, 26, 26, 0.12) !important; 
  opacity: 0;
  transform: translateY(40px);
  transition: opacity 0.8s cubic-bezier(0.16, 1, 0.3, 1), 
              transform 0.8s cubic-bezier(0.16, 1, 0.3, 1),
              box-shadow 0.5s ease;
  will-change: opacity, transform;
}

/* Состояние карточки после появления на экране */
.catalog-grid .product-card.visible,
.product-card.visible { 
  opacity: 1 !important; 
  transform: translateY(0) !important;
}

/* Эффект парения при наведении — ИСПРАВЛЕНО (сборка больше не упадет) */
.catalog-grid .product-card.visible:hover,
.product-card.visible:hover { 
  transform: translateY(-6px) !important; 
  box-shadow: 0 20px 40px rgba(26, 26, 26, 0.04) !important; 
  transition-delay: 0s !important; 
}

/* Слайдер картинок внутри карточки */
.slider-area { 
  position: relative; 
  height: 380px; 
  overflow: hidden; 
  display: flex; 
  align-items: center; 
  justify-content: center; 
  width: 100%;
  border-top-left-radius: 10px;
  border-top-right-radius: 10px;
}

.card-img { 
  width: 100%; 
  height: 100%; 
  object-fit: cover; 
  transition: transform 0.8s cubic-bezier(0.16, 1, 0.3, 1), opacity 0.3s ease;
}

.product-card:hover .card-img { 
  transform: scale(1.03);
}

.card-img.img-fade { 
  opacity: 0.2; 
  transform: scale(0.97);
}

/* Элементы управления слайдером (Стрелки и точки) */
.card-badge { 
  position: absolute; 
  top: 15px; 
  left: 15px; 
  background-color: rgba(255, 255, 255, 0.9); 
  color: #1a1a1a; 
  padding: 5px 10px; 
  border: 1px solid #1a1a1a; 
  border-radius: 2px; 
  font-size: 10px; 
  text-transform: uppercase; 
  letter-spacing: 2px; 
  font-weight: bold; 
  z-index: 1;
}

.arrow { 
  position: absolute; 
  background: #FDFBF7; 
  font-size: 14px; 
  width: 36px; 
  height: 36px; 
  top: 50%; 
  transform: translateY(-50%); 
  border-radius: 50%; 
  display: flex; 
  align-items: center; 
  justify-content: center; 
  cursor: pointer; 
  color: #1a1a1a; 
  z-index: 2; 
  opacity: 0; 
  transition: all 0.3s ease;
}

.slider-area:hover .arrow { 
  opacity: 1;
}

.arrow.left { left: 15px; }
.arrow.right { right: 15px; }

.arrow:hover { 
  background-color: #1a1a1a; 
  color: #FDFBF7; 
  border-color: #1a1a1a;
} 

.slider-dots { 
  position: absolute; 
  bottom: 15px; 
  display: flex; 
  gap: 8px; 
  z-index: 2; 
  left: 50%; 
  transform: translateX(-50%);
}

.dot { 
  width: 8px; 
  height: 8px; 
  background-color: rgba(255, 255, 255, 0.5); 
  border-radius: 50%; 
  transition: all 0.3s ease;
}

.dot.active { 
  background-color: #1a1a1a; 
  transform: scale(1.2);
}

/* Контентная текстовая область карточки */
.card-info { 
  padding: 30px 24px; 
  text-align: center; 
  display: flex; 
  flex-direction: column; 
  flex-grow: 1; 
  justify-content: space-between;
}

.product-title { 
  font-family: "Playfair Display", serif;
  font-weight: 400;
  font-size: 20px; 
  letter-spacing: 0.04em;
  color: #1a1a1a;
  margin-bottom: 12px;
  text-align: center;
  min-height: 50px;
}

.product-weight { 
  font-family: "Montserrat", sans-serif;
  font-weight: 300;
  font-size: 11px; 
  text-transform: uppercase;
  letter-spacing: 0.15em;
  color: #8c8c8c; 
  margin-bottom: 16px;
}

.product-desc { 
  font-family: "Montserrat", sans-serif;
  font-weight: 300;
  font-size: 13px; 
  line-height: 1.6; 
  color: #555555; 
  margin: 0 10px 24px 10px; 
  height: auto; 
  min-height: 65px; 
  display: -webkit-box; 
  -webkit-box-orient: vertical; 
  -webkit-line-clamp: 3; 
  overflow: hidden; 
  text-overflow: ellipsis;
}

.price-tag { 
  font-family: "Montserrat", sans-serif;
  font-weight: 500;
  font-size: 15px; 
  text-transform: uppercase;
  letter-spacing: 0.1em;
  color: #1a1a1a;
  margin-top: auto;
}

/* ======================================================= */
/* 5. ПОДВАЛ (FOOTER) И СЛУЖЕБНЫЕ КНОПКИ                   */
/* ======================================================= */
.footer { 
  text-align: center; 
  padding: 60px 0; 
  border-top: 1px solid rgba(214, 179, 201, 0.2); 
  margin-top: 50px;
}

.contact-section h3 { 
  font-size: 16px; 
  font-family: 'Playfair Display', serif; 
  margin-bottom: 20px; 
  letter-spacing: 0.5px;
}

.links-box { 
  display: flex; 
  justify-content: center; 
  gap: 20px; 
  margin-bottom: 30px;
}

.link-btn { 
  padding: 10px 30px; 
  border-radius: 4px; 
  text-decoration: none; 
  font-weight: bold; 
  font-size: 13px; 
  background-color: #1a1a1a; 
  color: white; 
  transition: opacity 0.3s ease; 
  letter-spacing: 1px; 
  text-transform: uppercase;
  min-width: 140px; 
  text-align: center;
}

.link-btn:hover { 
  opacity: 0.8; 
}

.copyright { 
  font-size: 12px; 
  opacity: 0.6;
}

.btn-up { 
  position: fixed; 
  bottom: 30px; 
  right: 30px; 
  width: 50px; 
  height: 50px; 
  border-radius: 50%; 
  border: 1px solid rgba(26, 26, 26, 0.2); 
  background-color: #FDFBF7; 
  font-size: 16px; 
  cursor: pointer; 
  color: #1a1a1a; 
  box-shadow: 0 4px 20px rgba(0,0,0,0.02); 
  transition: all 0.3s cubic-bezier(0.25, 1, 0.5, 1); 
  display: flex; 
  align-items: center; 
  justify-content: center; 
  z-index: 10;
}

.btn-up:hover { 
  background-color: #1a1a1a; 
  color: #FDFBF7; 
  transform: translateY(-4px); 
  border-color: #1a1a1a;
}

/* ======================================================= */
/* 6. ПЛАВНЫЕ КАТЕГОРИЙНЫЕ АНИМАЦИИ VUE                    */
/* ======================================================= */
.catalog-list-move,
.catalog-list-enter-active,
.catalog-list-leave-active { 
  transition: all 0.6s cubic-bezier(0.25, 1, 0.5, 1);
}

.catalog-list-enter-from,
.catalog-list-leave-to { 
  opacity: 0; 
  transform: scale(0.98) translateY(15px);
}

.catalog-list-leave-active { 
  position: absolute;
  width: calc(50% - 20px) !important;
  z-index: 0;
}
/* ======================================================= */
/* 7. МОБИЛЬНАЯ АДАПТИВНОСТЬ (ЭКРАНЫ ДО 768px)            */
/* ======================================================= */
@media (max-width: 768px) { 

  /* ЖЕСТКИЙ КОНТУР ПРОТИВ ВЫЛЕТА ЗА ЭКРАН */
  html, body {
    overflow-x: hidden !important;
    max-width: 100% !important;
    width: 100% !important;
  }

  /* Баннер и сетка */
  .editorial-banner { 
    width: 100% !important;
    max-width: 100% !important;
    margin-bottom: 40px !important;
    overflow: hidden !important;
  }

  .editorial-grid { 
    grid-template-columns: 1fr !important; 
    min-height: auto !important;
    width: 100% !important;
  }

  /* Скрываем боковую картинку баннера */
  .image-block {
    display: none !important;
  }

  /* ТЕКСТОВЫЙ БЛОК: Сброс десктопных отступов и центрирование */
  .text-block { 
    display: flex !important; 
    flex-direction: column !important; 
    align-items: center !important;
    text-align: center !important;
    width: 100% !important; 
    max-width: 100% !important;   
    padding: 40px 24px !important; /* Внутренние безопасные отступы по бокам */
    margin: 0 !important;
    box-sizing: border-box !important; 
  }

  /* ИСПРАВЛЕНИЕ ЗАГОЛОВКА: Ограничение ширины для идеального переноса в 2 строки */
  .main-title { 
    font-size: 26px !important; 
    line-height: 1.35 !important; 
    color: #1a1a1a !important; 
    text-align: center !important;
    width: 100% !important;
    max-width: 320px !important;   /* Заставляет фразу переноситься ровно как на макете */
    margin: 0 auto !important;     /* Обнуляем маржины, отступ регулируется линией */
  }

  /* Склеиваем разбитые скриптом анимации буквы обратно в стабильные блоки */
  .main-title .word-wrap,
  .word-wrap {
    display: inline-block !important;
    overflow: visible !important;
    white-space: nowrap !important; /* Запрещает словам ломаться внутри себя */
  }

  .main-title .anim-word,
  .anim-word {
    display: inline-block !important;
    transform: none !important;
    animation: none !important;
  }

  /* ИСПРАВЛЕНИЕ ДЕКОРАТИВНОЙ ЛИНИИ: Добавляем воздух сверху и снизу */
  .divider { 
    width: 60px !important; 
    height: 1px !important; 
    background-color: #1a1a1a !important; 
    /* 25px сверху (отодвигает от текста) и 30px снизу (отодвигает от манифеста) */
    margin: 25px auto 30px auto !important; 
  }

  /* ИСПРАВЛЕНИЕ ТЕКСТА МАНИФЕСТА: Контраст и читаемость */
  .manifesto { 
    font-size: 14px !important; 
    line-height: 1.85 !important;  /* Делает текст более воздушным */
    color: #1a1a1a !important;     /* Насыщенный черный цвет вместо блеклого серого */
    max-width: 100% !important;   
    width: 100% !important;
    margin: 0 auto !important;
    text-align: center !important;
  }

  /* Подпись шефа */
  .signature { 
    font-size: 15px !important;
    color: #1a1a1a !important; 
    text-align: center !important;
    margin-top: 30px !important;
  }

  /* --- ОСТАЛЬНЫЕ ЭЛЕМЕНТЫ СТРАНИЦЫ (БЕЗ ИЗМЕНЕНИЙ) --- */
  
  /* Каталог в одну колонку */
  .catalog-grid { 
    grid-template-columns: 1fr !important; 
    gap: 20px !important;
  } 
  
  /* Контур у карточек товаров */
  .product-card {
    border: 1px solid rgba(26, 26, 26, 0.08) !important;

    box-sizing: border-box !important;
    transition-delay: 0s !important;
    transform: translateY(30px) !important;
  }

  /* Высота области фото карточки на телефоне */
  .slider-area {
    height: 260px !important;
  }

  .catalog-list-leave-active {
    position: absolute !important;
    width: 100% !important;
  }

  .links-box {
    flex-direction: column;
    align-items: center;
    gap: 10px;
  }

  /* Мобильные габариты для кнопки "Наверх" */
  .btn-up {
    bottom: 20px;
    right: 20px;
    width: 44px;
    height: 44px;
    font-size: 14px;
    background-color: rgba(253, 251, 247, 0.9);
    backdrop-filter: blur(4px);
  }
}
.categories-box, 
.tabs-container, 
.tabs-wrapper { 
  border: none !important;
  outline: none !important;
  background: transparent !important;
}


</style>
