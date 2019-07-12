<template>
  <div>
    <template v-if="content.img === undefined">
      <div class="email-box" @click="$emit('openDetailsModal', true, 'details', content)">
        <div class="ct-n-no">
          <div>{{category}}</div>
          <div>{{content.no}}</div>
        </div>
        <div class="box">
          <div class="email-n-date">
            <div class="email">{{content.email}}</div>
            <div class="date">{{content.updated_at}}</div>
          </div>
          <div class="title">{{title}}</div>
          <div class="contents">{{contents}}</div>      
        </div>
      </div>
    </template>
    <template v-else>
      <div class="ad-box">
        <div class="sponsored">Sponsored</div>
        <img :src="imgURL"/>
        <div class="title">{{title}}</div>
        <div class="contents">{{contents}}</div>      
      </div>
    </template>
  </div>
</template>


<script>
export default {
  name: 'content-box',
  props: [ 'content', 'categories'],
  mounted(){
  },
  computed: {
    category () {
      let ctname = ''
      this.categories.forEach(ct => {
        if( ct.no === this.content.category_no) {
          ctname = ct.name
        }
      })
      return ctname
    },
    title(){
      return this.content.title.slice(0, 20) + '...'
    },
    contents() {
      return this.content.contents.slice(0,100) + '...'
    },
    imgURL() {
      return `http://comento.cafe24.com/public/images/${this.content.img}`
    }
  },
  methods:{
    clickContent() {
      console.log('clickContent()')
    }
  }

}
</script>


<style scoped lang="less">
.email-box{
  border: 1px solid gray;
  margin: 15px 0;
  max-width: 700px;
  min-width: 300px;

  .ct-n-no{
    display: flex;
    justify-content: space-between;
    border-bottom: 1px solid gray;
    padding: 3px 10px;  
  } 
  .box{
    padding: 10px 10px;  
    .email-n-date{
      display: flex;    
      margin-bottom: 13px;
      .email{
        padding-right: 10px;
        border-right: 1px solid gray;
      }
      .date{
        padding-left: 10px;
      }
    }
    .title{
      margin-bottom: 5px;
      font-weight: bold;
    }
  }
  
}

.ad-box{
  border: 1px solid gray;
  padding: 20px;
  background: gainsboro;
  width: min-content;
  .sponsored{
    color: blue;
    margin-bottom: 10px;
  }
  .title{
    margin-bottom: 5px;
    font-weight: bold;
  }
  img{
    width: 350px;
    margin: 12px 0;
  }
}
</style>
