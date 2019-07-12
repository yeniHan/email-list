<template>
  <div id="app">
    <details-modal :content="clickedContent" :categories="categories" @closeDetailsModal="toggleModal" ></details-modal>
    <filter-modal :categories="categories" @storeFilters="storeFilters" @closeFilterModal="toggleModal"></filter-modal>
    <div id="content">
      <div id="ft-btn-n-ord-selt">
        <div id="filter-btn" @click="toggleModal(true, 'filter')">필터</div>
        <div id="ord-selector">
          <input name="ord" type="radio" value="asc" id="ord-asc" @change="changeOrd"/><label for="ord-asc" class="selected">오름차순</label>
          <input name="ord" type="radio" value="desc" id="ord-desc" @change="changeOrd"/><label for="ord-desc">내림차순</label>        
        </div>
      </div>
      <content-box v-for="content in contents" :key="content.img === undefined? content.no: null" :content="content" :categories="categories" @openDetailsModal="toggleModal"></content-box>

    </div>
  </div>
</template>

<script>
import ContentBox from './components/ContentBox.vue'
import FilterModal from './components/FilterModal.vue'
import DetailsModal from './components/DetailsModal.vue'

import axios from 'axios'

export default {
  name: 'App',
  components: { 
    'content-box': ContentBox, 
    'filter-modal': FilterModal,
    'details-modal': DetailsModal,
   } ,
  data (){
    return {
      contents: [],
      emails: [],
      ads: [],
      pageC: 1,
      pageCMax: 100,
      pageA: 1, 
      pageAMax: 10,
      ord: 'asc',
      categories: [],
      filters: [],
      diff: null,
      clickedContent: null
    }
  },
  async mounted(){
    await this.asyncGetContentsNads()

    await  axios({
      method: 'GET',
      url: `https://comento.cafe24.com/category.php`
    }).then(res => {
      if(res.data.code === 200){
        this.categories = res.data.list
      }else{
        alert('서버 장애')
      }
    })

    window.onscroll = () => {
      let height= document.body.scrollHeight - window.innerHeight + 61
      let pos = window.pageYOffset
      if(this.pageC < this.pageCMax){
        if( height - pos < 10 && height - pos >= 0 ){
          //(async function () {

            this.pageC = this.pageC + 1
            this.pageA = this.pageA < this.pageAMax ? this.pageA + 1: 1
            console.log('page:', this.pageA)
            //setTimeout(()=> {
             this.asyncGetContentsNads()
            //, 500})
          //})
        }
      }
      
    }
  },
  methods: {
    async asyncGetContentsNads (ord) {

      let newContents = []
      if(ord){
        this.pageC = 1
        this.pageA = 1
        let eRes = await axios({
          method: 'GET',
          url: `https://comento.cafe24.com/request.php?page=${this.pageC}&limit=10&ord=${this.ord}`
        })

        if(eRes.data.code === 200){
          this.emails =  eRes.data.list            
        }else{
          alert('서버 장애')
        }
        

        let aRes = await axios({
          method: 'GET',
          url: `https://comento.cafe24.com/ads.php?page=${this.pageA}&limit=2`
        })
        
        if(aRes.data.code === 200){
          this.ads = aRes.data.list
        }else{
          alert('서버 장애')
        }
            
        console.log('ord change new emails?:', this.emails)
        if(this.filters.length > 0){
          let filteredEmails = this.emails.filter(cnt=> this.filters.indexOf(cnt.category_no) !== -1)
          filteredEmails.forEach((mail, idx) => {
            newContents.push(mail)              
            if((idx + 1 )% 4 === 0 ) newContents.push(this.ads[(idx + 1 )/4 - 1])
          })
              
        }else{
          this.emails.forEach((mail, idx) => {
            newContents.push(mail)              
            if((idx + 1 )% 4 === 0 ) newContents.push(this.ads[(idx + 1 )/4 - 1])
          })
        }

        this.contents = newContents

      }else{
        let eRes = await axios({
          method: 'GET',
          url: `https://comento.cafe24.com/request.php?page=${this.pageC}&limit=10&ord=${this.ord}`
        })

        if(eRes.data.code === 200){
          this.emails =  this.emails.concat(eRes.data.list) 
          console.log('from server emails:', eRes.data.list)           
        }else{
          alert('서버 장애')
        }


        if(this.filters.length > 0){

          let limit = this.diff === 3 || this.diff === 4 ? 3: 2 
          let aRes = await axios({
            method: 'GET',
            url: `https://comento.cafe24.com/ads.php?page=${this.pageA}&limit=${limit}`
          })
          if(aRes.data.code === 200){
            this.ads = this.ads.concat(aRes.data.list)    
            console.log('from server ads:', aRes.data.list)           

          }else{
            alert('서버 장애')
          }
          let filteredEmails = eRes.data.list.filter(cnt=> this.filters.indexOf(cnt.category_no) !== -1)
          console.log('diff:', this.diff)
          console.log('filteredEmails len:', filteredEmails.length)
          let posInfo = {
            0: []
          }

          let adIdx;
          if(filteredEmails.length < 4 - this.diff ){
            this.diff = this.diff + filteredEmails.length
            this.contents = this.contents.concat(filteredEmails)
          }else{
          if(this.diff !== 4){
            
            filteredEmails.filter((email, idx) => {
              newContents.push(email)
              if((idx + 1 + this.diff)%4 ===  0){
                newContents.push(aRes.data.list[(idx + 1 + this.diff)/4 - 1])
                adIdx = newContents.length - 1
                console.log('adIdx:', adIdx)
                console.log('email:', email)                
                console.log('idx:', idx)
                console.log('ad:', aRes.data.list[(idx + 1 + this.diff )/4 - 1])
                console.log('ad idx:',(idx + 1 + this.diff )/4 - 1) 
              }
            })
          }else{
            newContents.push(aRes.data.list[0])
            filteredEmails.filter((email, idx) => {
              newContents.push(email)
              if((idx + 1 + this.diff)%4 === 0){
                newContents.push(aRes.data.list[(idx + 1 + this.diff )/4])
                adIdx = newContents.length - 1      
                console.log('email:', email)
                console.log('idx:', idx)
                console.log('ad:', aRes.data.list[(idx + 1 + this.diff )/4])
                console.log('ad idx:',(idx + 1 + this.diff )/4)           
              }
            })
          }
          console.log('adIdx:', adIdx)
          this.diff = newContents.length - 1 - adIdx
          console.log('new diff:', this.diff)
          }


        }else{

          let limit = this.pageC%2 === 1 && this.pageC !== 1? 3: 2 
          let aRes = await axios({
            method: 'GET',
            url: `https://comento.cafe24.com/ads.php?page=${this.pageA}&limit=${limit}`
          })
          if(aRes.data.code === 200){
            this.ads = this.ads.concat(aRes.data.list)    
            console.log('from server ads:', aRes.data.list)           

          }else{
            alert('서버 장애')
          }

          if(this.pageC === 1){
            eRes.data.list.forEach((mail, idx) => {
              newContents.push(mail)              
              if(idx === 3 || idx === 7) newContents.push(aRes.data.list[(idx + 1 )/4 - 1])
            })
          }
          else if(this.pageC%2 === 0 ){
            eRes.data.list.forEach((mail, idx) => {
              newContents.push(mail)              
              if(idx === 1 || idx == 5 ) {
                newContents.push(aRes.data.list[(idx + 3 )/4 - 1])
                console.log('idx:', idx)
                console.log('ad:', aRes.data.list[(idx + 3 )/4 - 1])
                console.log('ad idx:',(idx + 3 )/4 - 1)                
              }
            })
          }else{
            newContents.push(aRes.data.list[0])            
            eRes.data.list.forEach((mail, idx) => {
              newContents.push(mail)              
              if(idx === 3 || idx == 7) {
                newContents.push(aRes.data.list[(idx + 1 )/4])
                console.log('idx:', idx)                
                console.log('ad:', aRes.data.list[(idx + 1 )/4])
                console.log('ad idx:',(idx + 1 )/4)
              }
            })
          }
        }
        this.contents = this.contents.concat(newContents)
        
      }
      
      console.log('contetns:', this.contents)
      console.log('ads:', this.ads)
      console.log('mails:', this.emails)
    },
    changeOrd($event){
      console.log('changeOrd() target:', $event.target)
      let val = $event.target.value
      this.ord = val

      let opp = val === 'asc'? 'desc': 'asc'
      document.querySelector(`label[for=ord-${val}]`).classList.add('selected')      
      document.querySelector(`label[for=ord-${opp}]`).classList.remove('selected')
      this.asyncGetContentsNads(this.ord)
    },
    storeFilters(filters){
      console.log('storeFilters() filters', filters)
      if(filters.length === this.categories.length) this.filters = []
      else{
        this.filters = filters
        console.log('emails:', this.emails)
        let filteredEmails = this.emails.filter(e => filters.indexOf(e.category_no) !== -1) 
        console.log('filteredEmails:', filteredEmails)
        let filteredContents = []
        let adIdx; 
        filteredEmails.forEach((email, idx) => {
          filteredContents.push(email)
          
          if((idx + 1)%4 === 0) {
            filteredContents.push(this.ads[(idx + 1)/4 - 1])
            adIdx = filteredContents.length - 1
          }
        })
        this.contents = filteredContents
        this.diff = this.contents.length - 1 - adIdx
        console.log('storeFilters diff:', this.diff)
      }
    },
    toggleModal(open, kind, content = null){
      console.log('toggleModal() content:', content)
      console.log('el:', document.querySelector(`#wrapper-${kind}`))
      if(content){
        this.clickedContent = content
      }

      if(open) document.querySelector(`#wrapper-${kind}`).style.display = 'flex'
      else document.querySelector(`#wrapper-${kind}`).style.display = 'none'
    }
  }

}
</script>

<style scoped lang="less">

#app {

  #content{
    width: fit-content;
    margin: auto;
    margin-top: 60px;
    display: flex;
    flex-direction: column;
  }

  #ft-btn-n-ord-selt{
    display: flex;
    justify-content: space-between;
    #filter-btn{
      width: fit-content;
      border: 1px solid gray;
      font-weight: bold;
      padding: 2px 16px;
      cursor: pointer;
    }
    #ord-selector{
      //align-self: flex-end;
      input{
        display: none;
      }
      label{
        padding: 0 5px; 
        cursor: pointer;
        &.selected{
          color: red;
        }
      }
    }
  }
  
}
</style>
