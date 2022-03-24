<template>
    <div>
        <!-- Selector themes -->
        <Dropdown @change="$emit('themeChanged', selectedTheme)" class="small_dropdown" v-model="selectedTheme" :options="themes" optionLabel="name" placeholder="Select a theme:"/>
        <!-- Selector themes -->
        <!-- Selector fonts_sizes -->
        <Dropdown @change="$emit('fontSizeChanged', selectedFontSize)" v-model="selectedFontSize" :options="fonts_sizes" optionLabel="name" placeholder="Select a font size:"/>
        <!-- Selector fonts_sizes -->
    </div>
</template>

<script>
import Dropdown from 'primevue/dropdown';

export default {
    components:{
		Dropdown,
    },
    emits: ['themeChanged', 'fontSizeChanged'],
    data(){
        return {
            selectedTheme: null,
            themes: [
                {name: '3024-day', code: '3024-day'},
                {name: '3024-night', code: '3024-night'},
                {name: 'abcdef', code: 'abcdef'},
                {name: 'ambiance', code: 'ambiance'},
                {name: 'base16-dark', code: 'base16-dark'},
                {name: 'base16-light', code: 'base16-light'},
                {name: 'bespin', code: 'bespin'},
                {name: 'blackboard', code: 'blackboard'},
                {name: 'cobalt', code: 'cobalt'},
                {name: 'colorforth', code: 'colorforth'},
                {name: 'dracula', code: 'dracula'},
                {name: 'duotone-dark', code: 'duotone-dark'},
                {name: 'duotone-light', code: 'duotone-light'},
                {name: 'eclipse', code: 'eclipse'},
                {name: 'elegant', code: 'elegant'},
                {name: 'erlang-dark', code: 'erlang-dark'},
                {name: 'hopscotch', code: 'hopscotch'},
                {name: 'icecoder', code: 'icecoder'},
                {name: 'isotope', code: 'isotope'},
                {name: 'lesser-dark', code: 'lesser-dark'},
                {name: 'liquibyte', code: 'liquibyte'},
                {name: 'material', code: 'material'},
                {name: 'mbo', code: 'mbo'},
                {name: 'mdn-like', code: 'mdn-like'},
                {name: 'midnight', code: 'midnight'},
                {name: 'monokai', code: 'monokai'},
                {name: 'neat', code: 'neat'},
                {name: 'neo', code: 'neo'},
                {name: 'night', code: 'night'},
                {name: 'panda-syntax', code: 'panda-syntax'},
                {name: 'paraiso-dark', code: 'paraiso-dark'},
                {name: 'paraiso-light', code: 'paraiso-light'},
                {name: 'pastel-on-dark', code: 'pastel-on-dark'},
                {name: 'railscasts', code: 'railscasts'},
                {name: 'rubyblue', code: 'rubyblue'},
                {name: 'seti', code: 'seti'},
                {name: 'the-matrix', code: 'the-matrix'},
                {name: 'tomorrow-night-bright', code: 'tomorrow-night-bright'},
                {name: 'tomorrow-night-eighties', code: 'tomorrow-night-eighties'},
                {name: 'ttcn', code: 'ttcn'},
                {name: 'twilight', code: 'twilight'},
                {name: 'vibrant-ink', code: 'vibrant-ink'},
                {name: 'xq-dark', code: 'xq-dark'},
                {name: 'xq-light', code: 'xq-light'},
                {name: 'yeti', code: 'yeti'},
                {name: 'zenburn', code: 'zenburn'},
            ],
            selectedFontSize: null,
            fonts_sizes: [
                {name: '10px', code: '10px'},
                {name: '11px', code: '11px'},
                {name: '12px', code: '12px'},
                {name: '13px', code: '13px'},
                {name: '14px', code: '14px'},
                {name: '16px', code: '16px'},
                {name: '18px', code: '18px'},
                {name: '20px', code: '20px'},
                {name: '24px', code: '24px'},
                {name: '48px', code: '48px'},
            ],
        }
    },
}
</script>

<style>
    .small_dropdown { 
        font-size: 12px;
        padding: 4px;
    }
</style>

