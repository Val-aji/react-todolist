<script setup>
    import InputKeyboard from '../../components/InputKeyboard/InputKeyboard.vue';
    import NavigasiVue from '../../components/Navigasi/NavigasiVue.vue';
    import AccountVue from '../../components/Account/AccountVue.vue';
    import {useStoreKeyboard} from "../../stores/keyboard/store.js"
    import { storeToRefs } from 'pinia';
    import NavbarHP from '../../components/NavbarHP/NavbarHP.vue';
    import {ref, onBeforeMount, watch} from "vue"
    import CardKeyboard from './CardKeyboard.vue';

    const storeKeybard = useStoreKeyboard()
    const {setStatus, generateKata, handleInput, setIsActive, resetValue} = storeKeybard
    const {navbarHP, posisi, listKata, hasilTes, isActive} = storeToRefs(storeKeybard)
    
    onBeforeMount(() => {
        generateKata()
    })

    const waktu = ref(60);
    let batasWaktu;
    watch(isActive, (newValue) => {
        if(newValue) {
            batasWaktu = setInterval(() => {
            waktu.value--;
            }, 1000);
        }
    })
    
    
    const hasil = ref(null)
    const manipulationDone = ref(true)
    watch(waktu, newValue => {
        if(newValue <= 0) {
            clearInterval(batasWaktu)
            hasil.value = hasilTes
            setIsActive(false)
            generateKata()
            waktu.value = 60
            manipulationDone.value = false
            setTimeout(() => {
                manipulationDone.value = true
            }, 100)
        }
    })

</script>

<template>
    <NavigasiVue :listNavigasi="navbarHP" @setStatus="setStatus"/>
    <div id="Keyboard" class="dark:bg-dark dark:text-light bg-light text-dark h-[110vh] overflow-y-auto pb-[10vh]">
        <AccountVue class="pt-[10vh]" :posisi="posisi">
            <InputKeyboard class="my-[8vh]"/>    
        </AccountVue>
        <NavbarHP @setStatus="setStatus" :listNavbar="navbarHP" :posisi="posisi">
            <div class="cardKeyboard w-full preTablet:w-[80%] preTablet:ml-[15vw] mt-[5vh] relative ">
                <p class="absolute right-[2vw] top-[-5vh] text-lg">{{waktu.toString().length <= 1 ? '0'+waktu : waktu}}</p>
                <div class="containerCard w-full flex flex-wrap p-0 justify-center preHp:justify-start preHp:ml-[2vw]"  
                >
                    <p  
                        v-for="(kata, index) in listKata" :key="index" 
                        :class="kata.status === 'default'? 'text-slate-700 dark:text-slate-400': kata.status ? 'dark:text-green-500 text-green-700' : 'text-red-500 dark:text-red-600'"
                        class=" font-[POPPINS] w-[45%] preHp:w-[30%] preTablet:w-[30%]  mx-[3px] "
                    >
                        {{ kata.text }}
                    </p>
                </div>
            </div>
            <InputKeyboard 
            v-if="manipulationDone"
            class="my-[4vh]" 
            @handleInput="handleInput" @setIsActive="setIsActive" 
            />
            
        </NavbarHP>

        <CardKeyboard v-if="hasil" :posisi="posisi" :hasil="hasil" />
        
    </div>
</template>