<template>
  <div class="hello">
    <article id="launch-modal">
		<section class="hero is-primary is-bold">
			<div class="hero-body">
				<div class="container">
					<h1 class="title">Vue.js</h1>
					<h2 class="subtitle">Event click show Bulma modal order form</h2>
				</div>
			</div>
		</section>
    <section class="section">
      <div class="container">
        <div class="content has-text-centered">
          <p class="control">
            <button v-on:click="launch" class="button is-primary">Launch modal</button>
          </p>
          </div>
      </div>
    </section>
        
    <div class="modal" v-bind:class="{'is-active':isActive}">
      <div class="modal-background"></div>
        <div class="modal-content">
          <div class="box">
            <div class="content">
              <div class="left">
                <ul class="progress" v-if="stageNum != 5">
                  <li><a class="" :class="{ 'activated': stageNum > 1, 'current': stageNum === 0 || stageNum == 1 }" href="#">Авторизация</a></li>
                  <li><a class="" :class="{ 'activated': stageNum > 2, 'current': stageNum == 2 }" href="#">Адрес доставки</a></li>
                  <li><a class="" :class="{ 'activated': stageNum > 4, 'current': stageNum == 3 || stageNum == 4 }" href="#">Способ оплаты</a></li>
                </ul>

                <div class="page-phone" v-if="stageNum == 0">
                  <h1>Авторизация</h1>
                  <div class="description">
                    <p>Введите номер телефона в поле ниже или нажмите зарегистрироваться, если нет аккаунта.</p>
                  </div>
                  <masked-input class="input" type='phone' v-model="phone" mask="\+\380 (11) 111-1111" placeholder="Введіть ваш номер телефону" />
                  <small>Например +380 90 111 1111</small>
                  <div class="actions">
                    <button class="next" id="firstBlock" @click="isPhone && stageNum++" :class="{ 'disabled': !isPhone }">Продолжить</button>
                    <a class="back" href="#">Зарегистрироваться</a>
                  </div>
                </div>
                <div class="page-sms" v-else-if="stageNum == 1">
                  <h1>Авторизация</h1>
                  <div class="description">
                    <p>Мы отправили сообщение с кодом авторизации на номер <strong>{{ phone }}</strong>. Введите его в поле ниже.</p>
                  </div>
                  <input class="input" type='password' v-model="password" v-show="!showPass" maxlength="6" placeholder="Введите код из сообщения" />
                  <small><a href="#">Код не поступает</a></small>
                  <div class="actions">
                    <button class="next" id="secondBlock" @click="isValidSecureCode && stageNum++" :class="{ 'disabled' : !isValidSecureCode }">Продолжить</button>
                    <a class="back" href="#" @click.prevent="()=>{ stageNum = 0; }">Изменить номер телефона</a>
                  </div>
                </div> <!-- stageNum == 1" -->

                <div class="page-address" v-else-if="stageNum == 2">
                  <h1>Адреса доставки</h1>
                  <div class="page-address-list">
                    <div id="fAdd" class="branch" @click="selected = 1" :class="{highlight:selected == 1}">
                      <div class="title">ОТДЕЛЕНИЕ №999</div>
                      <p class="address"><strong>Адрес</strong>: г. Мухино, ул. Пушкина, д. 5</p>
                    </div>
                    <div class="add-branch">
                      <span>Добавить новый адрес</span>
                    </div>
                  </div>
                  <div class="actions">
                    <button class="next" @click="isAddressSelected && stageNum++" :class="{ 'disabled' : !isAddressSelected }">Продолжить</button>
                  </div>
                </div>
                <div class="page-bankcard" v-else-if="stageNum == 3">
                  <h1>Оплата</h1>
                  <div class="page-bankcard-list">
                    <div class="bankcard" @click="selectedCard = 1" :class="{highlight:selectedCard == 1}">
                      <span class="name">Карточка выплат</span>
                      <span class="number">4567 **** **** 2367</span>
                    </div>
                    <div class="add-bankcard">
                      <span>Добавить новую карточку</span>
                    </div>
                  </div>
                  <div class="actions">
                    <button class="next" @click="isBankCardSelected && stageNum++" :class="{ 'disabled' : !isBankCardSelected }">Продолжить</button>
                  </div>
                </div>
                <div class="page-3d-secure" v-else-if="stageNum == 4" >
                  <div class="secure">
                    <h1>Данные карточки</h1>
                    <form>
                      <label for="fname">Номер карточки:</label>
                      <input type="text" maxlength="16" placeholder="XXXX-XXXX-XXXX-XXXX">
                        <div class="data-cvv-info">
                          <div>  
                            <label>Срок действия: </label>
                            <input type="tel" maxlength="2"> / <input type="tel" maxlength="2">
                          </div> 
                          <div>  
                            <label>CVV: </label>
                            <input type="tel" maxlength="3">
                          </div> 
                        </div>
                    </form>
                    <button @click="()=>{stageNum = 5}" type="submit">Продолжить</button>
                  </div>
                </div>
                <div class="page-complete" v-else-if="stageNum == 5">
                  <div class="final">
                    <div class="final-box">
                      <img src="../assets/img/new-box.svg" alt="">
                    </div>
                    <div class="text">
                      <h2>Заказ оформлен!</h2>
                      <p>Мы направили подтверждение на ваш номер <strong>{{ phone }}</strong>. Пожалуйста, ожидайте.
                      </p>
                    </div>
                  </div>
                  <div class="actions">
                    <button class="next" @click="close">Закрыть</button>
                  </div>
                </div>
              </div>
              <div class="right">
                <div class="sale-block">
                  <img src="../assets/img/lego-sw.svg" alt="sale">
                  <div class="sale-info">
                    <p>Конструктор LEGO <br /> Star Wars <br /> Звезда смерти</p>
                    <p class="price">30 550 грн</p>
                  </div>
                </div>
                <div class="branch sidebar" v-if="selected">
                  <div class="title">ОТДЕЛЕНИЕ №999</div>
                  <p class="address"><strong>Адрес</strong>: г. Мухино, ул. Пушкина, д. 5</p>
                </div>
                <div class="bankcard sidebar" v-if="selectedCard">
                  <span class="name">Карточка выплат</span>
                  <span class="number">4567 **** **** 2367</span>
                </div>
              </div><!-- end right -->
            </div>
          </div>
        </div>
        <button @click="close" class="modal-close"></button>
    </div>
</article>
  </div>
</template>

<script>
import MaskedInput from "vue-masked-input"

export default {
  name: 'HelloWorld',
  data() { 
    return {
      isActive: false,
      stageNum: 0,
      email: "",
      phone: "",
      password: "",
      firstClick: false,
      showPass: false,
      selected: 0,
      selectedCard: 0,
      password2: ""
    }
  },
    computed: {
    isPhone () {
      return /^\+380\s\(\d{2}\)\s\d{3}\-\d{4}$/.test( this.phone )
    },
    isValidSecureCode () {
      return /^\d{6}$/.test( this.password )
    },
    isValidEmail () {
      return /^(\s*)?(\+)?([- _():=+]?\d[- _():=+]?){5,14}(\s*)?$/.test(
        this.phone
      )
    },
    isAddAddress () {
      return !! this.selected
    },
    isBankCardSelected () {
      return !! this.selectedCard
    },
    isAddressSelected () {
      return !! this.selected
    }
  },
  methods: {
    launch() {
      this.isActive = true
    },
    close() {
      this.isActive = false
    },
    reset: function( values ) {
      Object.assign( this.$data, initValues(), values )
    },
    secondBlock() {
      this.firstClick = true;
    },
    fAdd(){
      this.isActive = !this.isActive
    }
  },
  components: {
    MaskedInput
  }
}
</script>
