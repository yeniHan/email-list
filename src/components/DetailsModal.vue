<template>
    <div id="wrapper-details">
        <div id="details-modal">
            <img id="close-ic" :src="require('../assets/close-ic.png')" @click="$emit('closeDetailsModal', false, 'details')"/>
            <template v-if="content !== null">
                <div id="content">
                    <div class="ct-n-no">
                      <div>{{category}}</div>
                      <div>{{content.no}}</div>
                    </div>
                    <div class="box">
                      <div class="email-n-date">
                        <div class="email">{{content.email}}</div>
                        <div class="date">{{content.updated_at}}</div>
                      </div>
                      <div class="title">{{content.title}}</div>
                      <div class="contents">{{content.contents}}</div>      
                    </div>
                </div>
            </template>
        </div>
    </div>
</template>


<script>
export default {
    name: 'details-modal',
    props: [ 'content', 'categories'],
    data(){
        return {
        }
    },
    computed:{
        category () {
          let ctname = ''
          this.categories.forEach(ct => {
            if( ct.no === this.content.category_no) {
              ctname = ct.name
            }
          })
          return ctname
        }
    },
    methods: {
      
    }
}

</script>

<style scoped lang='less'>
    #wrapper-details{
        display: none;        
        background: rgba(0, 0, 0, 0.7);
        position: fixed;
        top: 0;
        width: 100%;
        height: 100%;
        align-items: center;
        justify-content: center;

        #details-modal{
            background: white;
            padding: 30px;
            max-width: 800px;
            min-width: 300px;

            #close-ic{
                width: 32px;
                position: relative;
                top: -28px;
                left: 795px;
                cursor: pointer;
            }

            >div{
                margin-top: -30px;
            }
            #content{
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
        }
    }
</style>