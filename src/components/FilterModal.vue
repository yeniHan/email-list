<template>
    <div id="wrapper-filter">
        <div id="filter-modal">
            <img id="close-ic" :src="require('../assets/close-ic.png')" @click="closeThisModal"/>
            <div>
                <div id="title">필터</div>
                <div id="inputs" >
                    <div v-for="(category, idx) in categories"> 
                        <input @change="selectFilters" type="checkbox" name="categories" :value="category.no" :id="'ip-' + category.name"/><label :for="'ip-' + category.name">{{category.name}}</label>
                    </div>
                </div>
                <button @click="storeFilters">저장</button>
            </div>
        </div>
    </div>
</template>


<script>
export default {
    name: 'filter-modal',
    props: [ 'categories'],
    data(){
        return {
            filters: [],
            storedFilters: false
        }
    },
    methods: {
      selectFilters($event){
          let val = $event.target.value
          let checked = $event.target.checked
          if(checked){
              this.filters.push(val)
          }else{
              this.filters.splice(this.filters.indexOf(val), 1)
          }
          this.storedFilters = false
      },
      storeFilters(){
          this.storedFilters = true
          this.$emit('storeFilters', this.filters)
      },
      closeThisModal(){
        if(!this.storedFilters) {
            let ips = document.querySelectorAll('input[name="categories"]')
            ips.forEach(ip=>{
                ip.checked = false
            }) 
        }
        this.$emit('closeFilterModal', false, 'filter')          
      }
    }
}

</script>

<style scoped lang='less'>
    #wrapper-filter{
        display: none;        
        background: rgba(0, 0, 0, 0.7);
        position: fixed;
        top: 0;
        width: 100%;
        height: 100%;
        align-items: center;
        justify-content: center;

        #filter-modal{
            background: white;
            padding: 30px;
            max-width: 250px;
            min-width: 200px;

            #close-ic{
                width: 32px;
                position: relative;
                top: -28px;
                left: 223px;
                cursor: pointer;
            }

            >div{
                margin-top: -30px;
            }
            #title{
                font-size: 20px;
            }

            #inputs{
                >div{
                    margin-right: 5px;
                }
                margin: 18px 0;
                display: flex;
            }
            
            button{
                cursor: pointer;                
            }
        }
    }
</style>