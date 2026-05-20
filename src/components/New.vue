<template>
  <div class="site-wrapper">
    <div class="main-content-area">

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
              Я верю, что идеальный десерт — это не просто сахар и крем. Это точный расчет пропорций, геометрия слоев и честные локальные продукты. Каждое изделие создается вручную с бескомпромиссным вниманием к деталям. Как в точной механике, здесь нет места случайностям.
            </p>
            <div class="signature">Десерты в исполнении Надежды Динченко</div>
          </div>
          <div class="image-block">
            <img :src="bannerImg" alt="Безупречная структура десерта" class="cover-photo" />
          </div>
        </div>
      </header>

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

      <main class="catalog-grid">
        <TransitionGroup name="catalog-list">
          <div v-for="product in filteredProducts" :key="product.id" class="product-card">
            <div class="slider-area">
              <div v-if="product.badge" class="card-badge">{{ product.badge }}</div>
              
              <button v-if="product.images.length > 1" class="arrow left" @click="prevImage(product)">‹</button>
              
              <img 
                :src="product.images[product.currentImgIndex]" 
                :alt="product.title" 
                class="card-img" 
                :class="{ 'img-fade': animCardId === product.id }"
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
          </div>
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
import { ref, computed, onMounted, onUnmounted } from 'vue'
import bannerImg from '../assets/banner.jpg'
import eclairsImg from '../assets/eclairs.webp'
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
import macaroncutImg from '../assets/IMG_20260519_110510.webp'
import zaherImg from '../../IMG_20260509_120359 (1) 1.webp'
import macaronImg from '../../IMG_20260519_1105121.webp'

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
    images: [eclairsImg], 
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
    badge: 'Авторский взгляд',
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
const telegramUrl = ref('https://t.me/Ndeserts') 
const maxUrl = ref('https://max.ru/u/f9LHodD0cOL4iVq41cK4V4jnAVusNP_iDcj2fr8XLmeibZDGYM8iJq-Pa2k') 

</script>

<style scoped>
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

.action-bars {
  display: flex;
  justify-content: center;
  gap: 40px;
  margin-bottom: 60px;
  border-bottom: 1px solid rgba(26, 26, 26, 0.1);
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

.catalog-grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 40px;
  width: 100%;
  position: relative;
}



.slider-area {
  position: relative;
  height: 450px;
  overflow: hidden; 
  display: flex;
  align-items: center;
  justify-content: center;
  width: 100%;
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
  border: 1px solid rgba(26, 26, 26, 0.15);
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

.card-info { 
  padding: 25px;
  text-align: center;
  display: flex;
  flex-direction: column;
  flex-grow: 1; 
}

.product-title { 
  font-size: 17px;
  font-family: 'Playfair Display', serif; 
  margin: 0;
  letter-spacing: 0.5px;
  min-height: 50px;
}

.product-weight {
  font-size: 12px;
  color: #777;
  margin-bottom: 5px;
}

.product-desc {
  font-size: 13px;
  line-height: 1.6;
  color: #444;
  margin: 0 10px 15px 10px;
  height: 65px; 
  min-height: 65px;
  display: -webkit-box;
  -webkit-box-orient: vertical;
  -webkit-line-clamp: 3;
  overflow: hidden;
  text-overflow: ellipsis;
}

.price-tag {
  font-size: 18px;
  font-weight: bold;
  margin: auto 0 0 0; 
}

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
}

.link-btn:hover { opacity: 0.8; }

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

.cover-photo {
  width: 100%;
  height: 100%;
  object-fit: cover; 
  filter: grayscale(0.2) contrast(1.05); 
}

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
}

/* СТИЛИ АДАПТАЦИИ */
@media (max-width: 768px) {
  .catalog-grid { grid-template-columns: 1fr; }
  .links-box { flex-direction: column; align-items: center; gap: 10px; }
  
  .image-block { display: none !important; }
  
  .editorial-grid {
    grid-template-columns: 1fr;
    min-height: auto !important;
  }
  
  .text-block {
    padding: 40px 10px !important;
    text-align: center;
    align-items: center;
  }

  .divider {
    margin-left: auto;
    margin-right: auto;
  }
  
  .main-title { font-size: 32px; }
  .manifesto { margin: 0 auto; }
  .catalog-list-leave-active { position: static; }

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
/* ИДЕАЛЬНО РОВНАЯ КАРТОЧКА */
.product-card {
  background-color: #ffffff;
  border-radius: 0px; 
  display: flex !important;
  flex-direction: column !important;
  justify-content: space-between !important; /* Намертво прижимает кнопку к низу */
  height: 100% !important; /* Выравнивает все карточки в ряду по одной линии */
  overflow: hidden !important;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.01);
  transition: transform 0.5s cubic-bezier(0.16, 1, 0.3, 1);
}

.product-card:hover {
  transform: translateY(-4px);
}

/* ФИКСИРОВАННАЯ ЗОНА ДЛЯ ФОТО */
.product-card .slider-area {
  width: 100% !important;
  height: 350px !important; /* Одинаковый крупный журнальный формат */
  overflow: hidden !important;
  position: relative !important;
  background-color: #ffffff !important;
}

/* КАРТИНКА БЕЗ СЕРЫХ ПОЛЕЙ */
.product-card .card-img {
  width: 100% !important;
  height: 100% !important;
  object-fit: cover !important; /* Намертво убирает любые полосы */
  object-position: center !important; /* Фокус строго по центру десерта */
  display: block !important;
}

/* ВЫРАВНИВАНИЕ ТЕКСТА И КНОПОК */
.product-card .card-info {
  display: flex !important;
  flex-direction: column !important;
  flex-grow: 1 !important;
  justify-content: space-between !important;
  padding: 30px 24px !important; /* Просторные премиальные отступы */
}
/* НАЗВАНИЕ ДЕСЕРТА */
.product-card .product-title, 
.product-card h3 {
  font-family: "Playfair Display", "Didot", "Bodoni MT", serif !important; /* Элегантный засечечный шрифт */
  font-weight: 400 !important;
  font-size: 20px !important;
  letter-spacing: 0.04em !important; /* Легкое премиальное разрежение */
  color: #1a1a1a !important;
  margin-bottom: 12px !important;
  text-align: center !important;
}

/* ВЕС / РАЗМЕР */
.product-card .product-meta {
  font-family: "Montserrat", "Helvetica Neue", sans-serif !important;
  font-weight: 300 !important;
  font-size: 11px !important;
  text-transform: uppercase !important; /* Мелкий строгий апперкейс */
  letter-spacing: 0.15em !important; /* Широкий отступ между буквами */
  color: #8c8c8c !important;
  margin-bottom: 16px !important;
  text-align: center !important;
}

/* ОПИСАНИЕ СОСТАВА */
.product-card .product-desc,
.product-card p {
  font-family: "Montserrat", "Helvetica Neue", sans-serif !important;
  font-weight: 300 !important;
  font-size: 13px !important;
  line-height: 1.6 !important; /* Просторный интерлиньяж, чтобы текст «дышал» */
  color: #555555 !important;
  text-align: center !important;
  margin-bottom: 24px !important;
}

/* СТОИМОСТЬ И КНОПКА */
.product-card .product-price {
  font-family: "Montserrat", "Helvetica Neue", sans-serif !important;
  font-weight: 500 !important;
  font-size: 15px !important;
  text-transform: uppercase !important;
  letter-spacing: 0.1em !important;
  color: #1a1a1a !important;
  text-align: center !important;
  margin-top: auto !important; /* Выталкивает цену строго на нижнюю границу */
}
/* 1. НАСТРОЙКА КАРТОЧКИ (Скругление + триггер скролла) */
.product-card {
  background-color: #ffffff;
  border-radius: 10px !important; 
  display: flex !important;
  flex-direction: column !important;
  justify-content: space-between !important;
  height: 100% !important; 
  overflow: hidden !important;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.01);
  
  /* Эффект ховера */
  transition: transform 0.5s cubic-bezier(0.16, 1, 0.3, 1), box-shadow 0.5s ease !important;

  /* Скролл-анимация: привязываем появление к области видимости (view) */
  animation: revealCard linear both;
  animation-timeline: view();
  animation-range: entry 0% cover 30%; /* Начинает проявляться при входе на экран и заканчивется на 30% высоты окна */
}

/* Ховер-эффект */
.product-card:hover {
  transform: translateY(-6px) !important;
  box-shadow: 0 20px 40px rgba(26, 26, 26, 0.04);
}

/* 2. НАСТРОЙКА КАРТИНКИ И СЛАЙДЕРА (Идеальное скругление со всех сторон) */
.product-card .slider-area {
  width: 100% !important;
  height: 380px !important; 
  overflow: hidden !important;
  position: relative !important;
  background-color: transparent !important;
  border-radius: 10px !important; /* Скругляем картинку со всех четырех сторон */
}

.product-card .card-img {
  width: 100% !important;
  height: 100% !important;
  object-fit: cover !important; 
  display: block !important;
  border-radius: 10px !important; /* Скругляем картинку со всех четырех сторон */
}

/* 3. КАДРЫ ДЛЯ СКРОЛЛ-АНИМАЦИИ */
@keyframes revealCard {
  from {
    opacity: 0;
    transform: translateY(50px) scale(0.95);
  }
  to {
    opacity: 1;
    transform: translateY(0) scale(1);
  }
}



</style>
