<template>
    <div id="pd_theater_info">
        <h1 @click="go">공연장정보 페이지 {{this.$route.params.theaterId}}</h1>
        <v-flex xs16>
            <v-card dark color="indigo lighten-2">
            <v-card-text id="theater_name">공연장이름</v-card-text>
            </v-card>
        </v-flex>
       
        <v-card>
            <v-layout>
                <v-flex xs5>
                    <v-carousel 
                        cycle=false
                        delimiter-icon="stop"
                        prev-icon="keyboard_arrow_left"
                        next-icon="keyboard_arrow_right">
                            <v-carousel-item
                                v-for="(item,i) in items"
                                :key="i"
                                :src="item.src"
                            ></v-carousel-item>
                    </v-carousel>
                </v-flex>
                
                <v-flex xs7>
                        <div id="info">
                            <v-layout row wrap> <div class="info_title">위치</div> &nbsp;&nbsp;&nbsp; <div style="width=200px">{{theater_info.location}}</div> </v-layout>
                            <v-layout row wrap> <div class="info_title">시간</div> &nbsp;&nbsp;&nbsp; <div style="width=200px">{{theater_info.time}}</div> </v-layout>
                            <v-layout row wrap> <div class="info_title">수용가능인원</div> &nbsp;&nbsp;&nbsp; <div style="width=200px">{{theater_info.personnel}}</div> </v-layout>
                            <v-layout row wrap> <div class="info_title">부대시설</div> &nbsp;&nbsp;&nbsp; <div style="width=200px">{{theater_info.facilities}}</div> </v-layout>
                            <v-layout row wrap> <div class="info_title">무대면적</div> &nbsp;&nbsp;&nbsp; <div style="width=200px">{{theater_info.size}}</div> </v-layout>
                        </div>
                    </v-card-title>
                </v-flex>
            </v-layout>
                
            <v-divider light></v-divider>
            
            <v-card-actions id="register_btn_action">        
                <div id="rate">
                    <h3>극장 평점</h3>
                    <v-rating
                        left
                        v-model="getAvgRate"
                        :half-increments="true"
                        :readonly="true"
                        large
                    />
                </div>
                <v-dialog v-model="registerForm" persistent max-width="600px">
                    <v-btn absolute right id="register_btn" slot="activator" color="blue lighten-1">예약하기</v-btn>
                    <v-card>
                    <v-form ref="form">
                            <v-card-title>
                            <span class="headline">공연장 예약</span>
                            </v-card-title>
                            
                            <v-card-text>
                            <v-container grid-list-md>
                                <v-layout wrap>

                                <v-flex xs12 sm4>
                                    <v-autocomplete
                                    :items="['클래식','발레','판소리', '콘서트', '토크쇼', '뮤지컬', '연극', '오페라']"
                                    label="장르"
                                    v-model="register_info.category"
                                    :rules="[v => !!v || '장르를 선택해 주세요']"
                                    color="blue"
                                    required
                                    ></v-autocomplete>
                                </v-flex>

                                <v-flex xs12 sm4>
                                    <v-autocomplete
                                    :items="['서울특별시', '경기도', '인천광역시', '강원도', '대전광역시', '제주도', '광주광역시', '부산광역시', '울산광역시', '대구광역시']"
                                    label="지역"
                                    v-model="register_info.area"
                                    :rules="[v => !!v || '지역을 선택해 주세요']"
                                    required
                                    ></v-autocomplete>
                                </v-flex>

                                <v-flex xs12 sm4>
                                    <v-autocomplete
                                    :items="['0~50석', '50~100석', '100~200석', '200~400석', '400~600석', '600~800석', '800~1000석', '1000석 이상']"
                                    label="규모"
                                    v-model="register_info.size"
                                    :rules="[v => !!v || '규모를 선택해 주세요']"
                                    color="blue"
                                    required
                                    ></v-autocomplete>
                                </v-flex>
                                
                                <section id="date_picker">
                                     <v-layout row wrap>
                                        <v-flex xs12 sm6 md4>
                                        <v-menu
                                            ref="menu"
                                            v-model="menu"
                                            :close-on-content-click="false"
                                            :nudge-right="40"
                                            :return-value.sync="date"
                                            lazy
                                            transition="scale-transition"
                                            offset-y
                                            full-width
                                            min-width="290px"
                                        >
                                            <template v-slot:activator="{ on }">
                                            <v-text-field
                                                required
                                                v-model="register_info.startdate"
                                                label="공연시작일"
                                                prepend-icon="event"
                                                readonly
                                                v-on="on"
                                            ></v-text-field>
                                            </template>
                                            <v-date-picker v-model="register_info.startdate" no-title scrollable @change="change"></v-date-picker>

                                        </v-menu>
                                        </v-flex>
                                        <v-flex xs12 sm6 md4>
                                        <v-menu
                                            ref="menu"
                                            v-model="menu2"
                                            :close-on-content-click="false"
                                            :nudge-right="40"
                                            :return-value.sync="date"
                                            lazy
                                            transition="scale-transition"
                                            offset-y
                                            full-width
                                            min-width="290px"
                                        >
                                            <template v-slot:activator="{ on }">
                                            <v-text-field
                                                required
                                                v-model="register_info.enddate"
                                                label="공연종료일"
                                                prepend-icon="event"
                                                readonly
                                                v-on="on"
                                            ></v-text-field>
                                            </template>
                                            <v-date-picker v-model="register_info.enddate" no-title scrollable @change="change"></v-date-picker>
                                        </v-menu>
                                        </v-flex>
                                        <v-spacer></v-spacer>
                                     </v-layout>
                                <section>
                                </v-layout>
                            </v-container>
                            </v-card-text>

                            <v-card-actions>
                                <v-btn color="blue darken-1" flat v-on:click="registerForm = false">닫기</v-btn>
                                <v-spacer></v-spacer>
                                <v-btn color="blue darken-1" flat v-on:click="reset">초기화</v-btn>
                                <v-btn color="blue darken-1" flat v-on:click="submit">예약</v-btn>
                            </v-card-actions>
                        </v-form>
                    </v-card>
                </v-dialog>
            </v-card-actions>
            
        </v-card>
        <v-layout row wrap>
            <v-card>
                <v-flex>
                    <v-card-title primary-title style="margin-top:10px">
                        <div class="headline">대관 일정</div>
                    </v-card-title>
                <v-sheet width="700" height="500">
                    <v-calendar
                    :now="today"
                    :value="today"
                    color="primary"
                    >
                    <template v-slot:day="{ date }">
                        <template v-for="event in eventsMap[date]">
                        <v-menu
                            :key="event.title"
                            v-model="event.open"
                            full-width
                            offset-x
                        >
                            <template v-slot:activator="{ on }">
                            <div
                                v-if="!event.time"
                                v-ripple
                                class="my-event"
                                v-on="on"
                                v-html="event.title"
                            ></div>
                            </template>
                            <v-card
                            color="grey lighten-4"
                            min-width="350px"
                            flat
                            >
                            <v-toolbar
                                color="primary"
                                dark
                            >
                                <v-btn icon>
                                <v-icon>edit</v-icon>
                                </v-btn>
                                <v-toolbar-title v-html="event.title"></v-toolbar-title>
                                <v-spacer></v-spacer>
                                <v-btn icon>
                                <v-icon>favorite</v-icon>
                                </v-btn>
                                <v-btn icon>
                                <v-icon>more_vert</v-icon>
                                </v-btn>
                            </v-toolbar>
                            <v-card-title primary-title>
                                <span v-html="event.details"></span>
                            </v-card-title>
                            <v-card-actions>
                                <v-btn
                                flat
                                color="secondary"
                                >
                                Cancel
                                </v-btn>
                            </v-card-actions>
                            </v-card>
                        </v-menu>
                        </template>
                    </template>
                    </v-calendar>
                </v-sheet>
                </v-flex>
            </v-card>

            <v-card id="review">
                <v-card-text>
                    <v-card-title primary-title>
                            <div class="headline">후기</div>
                    </v-card-title>
                <v-expansion-panel>
                        <v-expansion-panel-content
                        v-for="review in reviews"
                        :key="review.title"
                        :pagination.sync="pagination"
                        rows-per-page-text
                        >
                        <template v-slot:header>
                            <div v-html="review.title"></div>
                            <v-rating align="right"
                                v-model="review.rate" 
                                :half-increments="true"
                                :readonly="true"/>
                        </template>
                        <v-card>
                            <v-card-text v-html="review.details"></v-card-text>
                        </v-card>
                        </v-expansion-panel-content>
                    </v-expansion-panel>
                </v-card-text>
            </v-card>
        </v-layout>

        <v-card>
            <v-card-title primary-title style="margin-top:10px">
                    <div class="headline">유사한 공연장</div>
            </v-card-title>

            <v-layout row wrap>
            <v-flex xs4 sm4>
                <v-card class="similar">
                    <v-img src="https://cdn.vuetifyjs.com/images/cards/sunshine.jpg" height="400px"/>

                    <v-card-title primary-title>
                        <div>
                            <div class="headline">유사1</div>
                            <span class="grey--text">1,000 miles of wonder</span>
                        </div>
                    </v-card-title>
                </v-card>
            </v-flex>

            <v-flex xs4 sm4>
                <v-card class="similar">
                    <v-img src="https://cdn.vuetifyjs.com/images/cards/sunshine.jpg" height="400px"/>

                    <v-card-title primary-title>
                        <div>
                            <div class="headline">유사2</div>
                            <span class="grey--text">1,000 miles of wonder</span>
                        </div>
                    </v-card-title>
                </v-card>
            </v-flex>

            <v-flex xs4 sm4>
                <v-card class="similar">
                    <v-img src="https://cdn.vuetifyjs.com/images/cards/sunshine.jpg" height="400px"/>

                    <v-card-title primary-title>
                        <div>
                            <div class="headline">유사3</div>
                            <span class="grey--text">1,000 miles of wonder</span>
                        </div>
                    </v-card-title>
                </v-card>
            </v-flex>
        </v-layout>
        </v-card>

    </div>
</template>

<script>
  export default {

    data () {
      return {

        date: new Date().toISOString().substr(0, 10),
        menu: false,
        menu2: false,

        registerForm: false,

        register_info: {
            category: '',
            size:'',
            area:'',
            startdate:'',
            enddate:''
        },

        theater_info:{
            location : "서울특별시 종로구 창경궁로 259 2층 창조소극장",
            time: "09:00 - 21:00",
            personnel:"100석",
            facilities:"무대, 음향, 무대기계, 연습, 피아노",
            size: "너비 6m * 깊이 5m * 높이 2.8m"
        },
        items: [
          {
            src: require('@/assets/images/theater1.png')
          },
          {
            src: require('@/assets/images/cat.jpg')
          },
          {
            src: require('@/assets/images/theater1.png')
          },
          {
            src: require('@/assets/images/cat.jpg')
          }
        ],
        reviews:[
               {
                    title: "너무 맘에 들어요",
                    details: "너무너무너무너무너무 맘에 들어요!! 끼아아악!",
                    rate: 4.5
                },
                {
                    title: "솔직히 좀 별로였습니다",
                    details: "생각보다 공연 시설이 낡아서 맘에 안들었어요. 공연장 사진도 최신으로 업데이트 해주세요ㅡㅡ",
                    rate: 1.5
                },
                {
                    title: "너무너무너무 맘에 들어요",
                    details: "너무너무너무너무너무 맘에 들어요!! 예에!",
                    rate: 3.5
                },
                {
                    title: "진짜 맘에 들어요",
                    details: "너무너무너무너무너무 맘에 들어요!! 꺄아아악!",
                    rate: 3.5
                },
                {
                    title: "정말 맘에 들어요",
                    details: "너무너무너무너무너무 맘에 들어요!! 꿀잼!",
                    rate: 4.5
                },
                
        ],
        today: new Date().getDate().toString,
        events: [
        {
          title: 'Vacation',
          details: 'Going to the beach!',
          date: '2018-12-30',
          open: false
        },
        {
          title: 'Vacation',
          details: 'Going to the beach!',
          date: '2018-12-31',
          open: false
        },
        {
          title: 'Vacation',
          details: 'Going to the beach!',
          date: '2019-01-01',
          open: false
        },
        {
          title: 'Meeting',
          details: 'Spending time on how we do not have enough time',
          date: '2019-01-07',
          open: false
        },
        {
          title: '30th Birthday',
          details: 'Celebrate responsibly',
          date: '2019-01-03',
          open: false
        },
        {
          title: 'New Year',
          details: 'Eat chocolate until you pass out',
          date: '2019-01-01',
          open: false
        },
        {
          title: 'Conference',
          details: 'Mute myself the whole time and wonder why I am on this call',
          date: '2019-01-21',
          open: false
        },
        {
          title: 'Hackathon',
          details: 'Code like there is no tommorrow',
          date: '2019-02-01',
          open: false
        }
      ],

      }
    },
    
    computed: {
        getAvgRate(){
            var sum = 0;
            var array = this.reviews;
            for(var i=0;i<array.length;i++){
                sum += array[i].rate;
            }
            return sum/array.length;
        },
        eventsMap () {
            const map = {}
            this.events.forEach(e => (map[e.date] = map[e.date] || []).push(e))
            return map
        }
    },
    methods :{
        open (event) {
            alert(event.title)
        },
        onClickRegister(){
            alert("예약하기버튼");

        },
        
        submit () {
            if (this.$refs.form.validate()) {
                alert(this.register_info.area +  " / " + this.register_info.size)
                this.registerForm = false;      
            }
        },
        reset () {
            return this.$refs.form.reset()
        },
        change (){
            this.menu = false
            this.menu2 = false
        },
        go(){
          alert(this.$route.params.theaterId)
        }
    },

    created(){
      this.alert()
    }

  }
</script>

<style>
#pd_theater_info{
    margin-left: 50px;
    margin-right: 50px;
    margin-bottom: 20px;
    text-align: center;
}
#theater_name{
    font-size: 35px;
}

#info{
    width: 750px;
    font-size: 20px;
    margin-left: 10px;
    margin-top: 20px;
}

#register_btn_action{
    height: 155px;
    margin-top:-70px;
    margin-bottom: 10px
}

#register_btn{
    width:400px;
    height: 70px;
    font-size: 35px;
}

#rate{
    margin-left: 170px;
    margin-top: 70px;
}

.info_title{
    width: 120px;
    text-align: left;

}

.similar{
    height: 600px;
    width: 400px;
    margin-left: 35px;
}

#review{
    width: 700px;
}

.my-event {
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
    border-radius: 2px;
    background-color: #1867c0;
    color: #ffffff;
    border: 1px solid #1867c0;
    width: 100%;
    font-size: 12px;
    padding: 3px;
    cursor: pointer;
    margin-bottom: 1px;
  }
</style>

