<template>
    <div class="mx-w-screen-lg p-5">
        <div class="grid grid-cols-2 pt-6">
            <div class="w-11/12 mx-auto">
                <div class="flex justify-center w-full py-2">
                    <input v-model="filterConfig.text" class="w-11/12 border rounded-l-lg" placeholder="Search" @keydown="getEmails()">
                    <button class=" bg-indigo-400 py-2 px-10 text-white rounded-r-lg" @click="getEmails()">
                        <v-icon name="fa-search"></v-icon>
                    </button>
                </div>
                <div class="rounded-md border-2 border-indigo-500 overflow-x-auto scroll-smoth scroll-eddie " style="max-height: 70vh; height: 70vh;">
                    <table class=" border-collapse w-full">
                        <thead class="sticky top-0">
                            <tr class="bg-indigo-400 text-white">
                                <th class="cursor-pointer w-1/4 max-w-1/4 py-2" v-for="column in tableColumns" @click="sortColumn(column)">
                                    {{ column }}
                                    <v-icon v-if="column.toLowerCase() == keywordSort" name="fa-sort-down"></v-icon>
                                    <v-icon v-else-if="'-'+column.toLowerCase() == keywordSort" name="fa-sort-up"></v-icon>
                                    <v-icon v-else name="fa-sort"></v-icon>
                                </th>
                            </tr>
                        </thead>
                        <tbody v-if="!loadingMails">
                            <tr class="cursor-pointer hover:bg-gray-300" :class="highLightRowClass(email)" v-for="email in emails" @click="setEmail(email)">
                                <td class="max-w-0 truncate py-2">{{ email.from }}</td>
                                <td class="max-w-0 truncate">{{ email.to }}</td>
                                <td class="max-w-0 truncate">{{ email.subject }}</td>
                                <td class="max-w-0 truncate">{{ email.directory }}</td>
                            </tr>
                        </tbody>
                    </table>
                    <div class="flex justify-center size-full text-center" v-if="loadingMails">
                        <h4 class="my-auto ">
                            <v-icon class="text-indigo-500" name="fa-spinner" scale="2" animation="spin-pulse"></v-icon>
                            Loading...
                        </h4>
                    </div>
                </div>
                <div class="flex justify-between text-indigo-500 pt-1" v-if="!loadingMails">
                    <v-icon name="fa-arrow-alt-circle-left" scale="1.5" class="cursor-pointer" @click="changePage('back')"></v-icon>
                    {{ currentPage }}  / {{ Math.ceil(totalEmails/40) }}
                    <v-icon name="fa-arrow-alt-circle-right" scale="1.5" class="cursor-pointer" @click="changePage('next')"></v-icon>
                </div>
            </div>
            <div class="flex flex-col justify-left my-auto size-full">
                <template v-if="selectedEmail">
                    <h2 class="text-xl font-bold py-5">{{ selectedEmail.subject }}</h2>
                    <div class="rounded-lg border border-indigo-500 w-11/12 h-5/6 my-auto p-6 h-f scroll-eddie" style="max-height: 80vh; overflow-x: hidden;">
                        <p><b>From:</b> {{ selectedEmail.from }} </p>
                        <p><b>To:</b> {{ selectedEmail.to }} </p>
                        <br>
                        <p><b>Subject:</b> {{ selectedEmail.subject }} </p>
                        <br>
                        {{ selectedEmail.content }}
                        <br>
                        <br>
                        <p><b>Locate:</b> {{ selectedEmail.directory }} </p>
                    </div>
                </template>
            </div>
        </div>
    </div>
</template>

<script setup>
import { ref } from 'vue';
import axios from "../utils/axios"
import { onMounted } from 'vue';
import Swal from 'sweetalert2';

const selectedEmail = ref(null)
let emails = ref([]);
let totalEmails = ref(0)
let currentPage = ref(1)
let loadingMails = ref(false)
let tableColumns = ['From', 'To', 'Subject', 'Directory']
let keywordSort = ref("_score")
let filterConfig = ref({
    text: null,
    column: null,
})

function sortColumn(column){
    let keyword = column.toLowerCase()
    if ('-'+keyword == keywordSort.value){
        keywordSort.value='_score'
    } else if (keyword == keywordSort.value){
        keywordSort.value='-'+keyword
    } else {
        keywordSort.value=keyword
    }
    getEmails()
}

/*Methods*/
function setEmail(email){
    selectedEmail.value=email
}
function highLightRowClass(email){
    return selectedEmail.value?.id == email.id ? 'bg-gray-400' : ''
}

function changePage(source){
    console.log(totalEmails.value/40)
    if (source == 'next'){
        currentPage.value++
        if ( currentPage.value > Math.ceil(totalEmails.value/40) ){
            Swal.fire({
                icon: "warning",
                text: "No more pages"
            })
            currentPage.value--
        }
    } else if (source == 'back'){
        currentPage.value--
        if ( currentPage.value <= 0 ){
            Swal.fire({
                icon: "warning",
                title: "No more pages"
            })
            currentPage.value ++
        }
    }
    getEmails('pagination')
}

async function getEmails(source='filter'){
    loadingMails.value=true
    let textQuery=null
    if (source == "filter"){
        currentPage.value = 1
    }
    if (filterConfig.value?.text){
        textQuery = (filterConfig.value.text).trim().replaceAll(' ','+')
    }
    if (keywordSort.value != '_score'){
        let columnToFilter = keywordSort.value.replaceAll("-","")
        textQuery = textQuery ? columnToFilter+":"+textQuery : null
    }
    let params = {
        text: textQuery,
        start: (currentPage.value - 1) * 40,
        step: 40,
        order_by: keywordSort.value
    }
    let res = await axios.post("/get_emails", params, ); 
    emails.value = []    
    emails.value = res.data.Hits.map((hit)=>{
        return {
            id: hit._source._id,
            directory: hit._source.Directory,
            content: hit._source.Content,
            from: hit._source.From,
            to: hit._source.To,
            subject: hit._source.Subject
        }    
    })
    totalEmails.value = res.data.Total.Value
    selectedEmail.value = emails.value[0]
    loadingMails.value=false
}

onMounted(() => {
    getEmails()
})
</script>

<style lang="scss" scoped>
.scroll-eddie::-webkit-scrollbar {
  width: 5px;
  height: 10%;
  display: block;
}

.scroll-eddie::-webkit-scrollbar-thumb {
  background: #818CF8;
  border-radius: 4px;
  transition: background 0.3s, width 0.3s; /* Agrega transición para suavizar el cambio de color y ancho */

  &:hover {
    background: #6366F1;
    box-shadow: 0 0 2px 1px rgba(0, 0, 0, 0.2);
    ::-webkit-scrollbar{
        width: 20px; /* Modifica el ancho al hacer hover */
    }
  }
}
</style>
