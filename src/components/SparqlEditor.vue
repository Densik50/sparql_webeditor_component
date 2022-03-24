<template>
	<div>
		<Menubar :model="editorBarItems" class="editor_bar"/>
		<TabMenu @tab-change="tabChanged" :model="tabs_list" v-model:activeIndex="tabSelected"/>

		<Dialog header="Prefixes manager" v-model:visible="displayPrefixes"
			:style="{width: '75vw'}" :maximizable="true"
			:modal="false" :draggable="false" ref="prefixesdialog">
			<PrefixesDialog @insertentry="insertEntryIntoCode"/>
		</Dialog>
		<Dialog header="Preferences" v-model:visible="displayPreferences"
			:style="{width: '75vw'}" :maximizable="true"
			:modal="false" :draggable="false" ref="preferencesdialog">
			<PreferencesDialog @fontSizeChanged="changeFontSize" @themeChanged="changeTheme"/>
		</Dialog>

		<textarea v-model="contents[tabSelected]" id="editor"></textarea>
		<div class="editor_footer"> Ln {{this.currentLineNumber}}, Col {{this.currentColNumber}}</div>
	</div>
</template>

<script>
import * as CodeMirror from 'codemirror';
import 'codemirror/lib/codemirror.css';
import 'codemirror/mode/sparql/sparql.js';
import 'codemirror/addon/hint/show-hint.css';
import 'codemirror/addon/hint/show-hint.js';
import 'codemirror/addon/hint/anyword-hint.js';
import 'codemirror/addon/edit/matchbrackets.js';
import 'codemirror/addon/edit/closebrackets.js';

//import 'codemirror/theme/midnight.css';
import 'codemirror/theme/3024-day.css'
import 'codemirror/theme/3024-night.css'
import 'codemirror/theme/abcdef.css'
import 'codemirror/theme/ambiance.css'
import 'codemirror/theme/base16-dark.css'
import 'codemirror/theme/base16-light.css'
import 'codemirror/theme/bespin.css'
import 'codemirror/theme/blackboard.css'
import 'codemirror/theme/cobalt.css'
import 'codemirror/theme/colorforth.css'
import 'codemirror/theme/dracula.css'
import 'codemirror/theme/duotone-dark.css'
import 'codemirror/theme/duotone-light.css'
import 'codemirror/theme/eclipse.css'
import 'codemirror/theme/elegant.css'
import 'codemirror/theme/erlang-dark.css'
import 'codemirror/theme/hopscotch.css'
import 'codemirror/theme/icecoder.css'
import 'codemirror/theme/isotope.css'
import 'codemirror/theme/lesser-dark.css'
import 'codemirror/theme/liquibyte.css'
import 'codemirror/theme/material.css'
import 'codemirror/theme/mbo.css'
import 'codemirror/theme/mdn-like.css'
import 'codemirror/theme/midnight.css'
import 'codemirror/theme/monokai.css'
import 'codemirror/theme/neat.css'
import 'codemirror/theme/neo.css'
import 'codemirror/theme/night.css'
import 'codemirror/theme/panda-syntax.css'
import 'codemirror/theme/paraiso-dark.css'
import 'codemirror/theme/paraiso-light.css'
import 'codemirror/theme/pastel-on-dark.css'
import 'codemirror/theme/railscasts.css'
import 'codemirror/theme/rubyblue.css'
import 'codemirror/theme/seti.css'
import 'codemirror/theme/the-matrix.css'
import 'codemirror/theme/tomorrow-night-bright.css'
import 'codemirror/theme/tomorrow-night-eighties.css'
import 'codemirror/theme/ttcn.css'
import 'codemirror/theme/twilight.css'
import 'codemirror/theme/vibrant-ink.css'
import 'codemirror/theme/xq-dark.css'
import 'codemirror/theme/xq-light.css'
import 'codemirror/theme/yeti.css'
import 'codemirror/theme/zenburn.css'

import {PrimeIcons} from 'primevue/api';
import Menubar from 'primevue/menubar';
import Dialog from 'primevue/dialog';
import PrefixesDialog from '@/components/PrefixesDialog.vue'
import PreferencesDialog from '@/components/PreferencesDialog.vue'
import TabMenu from 'primevue/tabmenu';

export default {
  name: 'SparqlEditor',
  props: {
    codeValue: String,
  },
  components:{
		Menubar,
		TabMenu,
		Dialog,
		PrefixesDialog,
		PreferencesDialog,
  },
  data(){
		return{
			theme: 'rubyblue',
			currentLineNumber: "",
			currentColNumber: "",
			contents: [
				'PREFIX a: <http://www.w3.org/2000/10/annotation-ns#>\nPREFIX dc: <http://purl.org/dc/elements/1.1/>\nPREFIX foaf: <http://xmlns.com/foaf/0.1/>\nPREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>\n # Comment!\n SELECT ?given ?family\nWHERE {\n{\n?annot a:annotates <http://www.w3.org/TR/rdf-sparql-query/> .\n?annot dc:creator ?c .\nOPTIONAL {?c foaf:givenName ?given ;\nfoaf:familyName ?family }\n} UNION {\n?c !foaf:knows/foaf:knows? ?thing.\n?thing rdfs\n} MINUS {\n?thing rdfs:label "剛柔流"@jp\n}\nFILTER isBlank(?c)\n}\n',
			],
			tabs_list: [
				{label: 'Main'},
			],
			tabsAmount: 1,
			tabSelected: 0,
			displayPrefixes: false,
			displayPreferences: false,
			show_querry_panel: false,
			position: 'center',
			editorBarItems: [
				{
					label:'Prefixes',
					icon: PrimeIcons.DATABASE,
					command: () => {
					this.displayPrefixes = true;
					},
				}, {
					label:'Panel',
					icon:'',
					command: () => {
						if(this.show_querry_panel == true){
							this.show_querry_panel = false;
						}else{
							this.show_querry_panel = true;
						}
						this.$emit('hideshow_querry_panel', this.show_querry_panel);
					},
				}, {
					label: 'Preferences',
					icon: PrimeIcons.USER,
					command: () => {
						this.displayPreferences = true
					},
				}, {
					label: 'Add Tab',
					icon:'',
					command: () => {
						this.addTab()
					},
				}],
		}
  },
  mounted(){
	this.cm = CodeMirror.fromTextArea(document.getElementById('editor'), {
		id: "codemirror",
		lineNumbers: true,
		theme: this.theme,
		mode: 'sparql',
		autofocus: true,
		matchBrackets: true,
		autoCloseBrackets: true,
		lineWrapping: true,
		extraKeys: {"Ctrl-Space": "autocomplete" },
		completeSingle: false,
		hintOptions: {
			completeSingle:false,
		}});

	var scope_ = this
	this.cm.on("keyup", function (cm) {
        scope_.contents[scope_.tabSelected] = cm.getValue()
    });
	
    const keyWords=[
			"BASE", "PREFIX", "SELECT", "ASK", "CONSTRUCT", "DESCRIBE", "DISTINCT", "REDUCED",
			"FROM", "NAMED", "WHERE", "GRAPH", "UNION", "FILTER", "OPTIONAL", "ORDER",
			"LIMIT", "OFFSET", "BY", "ASC", "DESC", "STR", "LANG", "LANGMATCHES",
			"DATATYPE", "BOUND", "SAMETERM", "ISIRI", "ISURI", "ISBLANK", "ISLITERAL", "REGEX",
			"CONCAT", "BIND", "NOT", "EXISTS", "MINUS", "AS", "VALUES", "UNDEF",
			"GROUP", "HAVING", "SUM", "AVG", "REDUCED"];

		CodeMirror.hint.anyword = function (editor) {
				//get cursor and current line
				const cursor = editor.getCursor();
				const currentLine = editor.getLine(cursor.line);
				let start = cursor.ch;
				let end = start;
				//regex for matching keywords
				const regex=/[\w.]/;
				while (end < currentLine.length && regex.test(currentLine.charAt(end))) ++end;
				while (start && regex.test(currentLine.charAt(start - 1))) --start;
				const current = start !== end && currentLine.slice(start, end);
				//return hints from original function or emptylist
				const result = {list: []};
				result.to=CodeMirror.Pos(cursor.line, end);
				result.from=CodeMirror.Pos(cursor.line, start);
				//push our matching keywords into hint list
				keyWords.forEach(h=>{if (h.startsWith(current)) result.list.push(h);});
				//removes duplicates
				result.list = [...new Set(result.list)];
				//sort list
				result.list.sort();
				return result;
		};
		//AUTO COMPLETE EDIT END

		//updates
		this.cm.on("cursorActivity", this.updateInfo);
		this.updateInfo();
  },
  methods: {
    updateInfo() {
			const cursor = this.cm.getCursor();
			this.currentLineNumber = cursor.line + 1;
			this.currentColNumber = cursor.ch;
    },
	addTab(){
		this.tabsAmount += 1;
		this.tabs_list.push({label: 'Tab ' + this.tabsAmount})
		this.contents.push('')
	},
	insertEntryIntoCode(){

	},
	changeTheme(theme){
		console.log(theme)
		this.cm.setOption("theme", theme.code)
	},
	changeFontSize(font_size){
		console.log(font_size)
		// var cols = document.getElementsByClassName('Codemirror');
		// let i = 0
		// for(i = 0; i < cols.length; i++) {
		// 	cols[i].style.font_size = font_size.code;
		// }
	},
	tabChanged(event){
		this.cm.setValue(this.contents[event.index])
	}
  }
}
</script>

<style>
	.p-button {
		margin: 0.3rem .5rem;
		min-width: 10rem;
	}

	p {
		margin: 0;
	}

	.confirmation-content {
		display: flex;
		align-items: center;
		justify-content: center;
	}

	.p-dialog .p-button {
		min-width: 6rem;
	}

	.editor_bar {
		height: 4vh;
		background: #0F192A;
		border-bottom: 1px solid #ddd !important;
		background-color: rgb(15, 25, 42) !important;
		color: #ddd;
    }

    .main {
			height: 85vh;
    }

    .editor_footer {
			background: #0F192A;
			border-top: 1px solid #ddd;
			color: #ddd;
    }

    .p-menuitem {
        z-index: 10;
    }

    .table-header {
    display: flex;
    align-items: center;
    justify-content: space-between;

    @media screen and (max-width: 960px) {
        align-items: start;
	}
}

.confirmation-content {
    display: flex;
    align-items: center;
    justify-content: center;
}
@media screen and (max-width: 960px) {
	::v-deep(.p-toolbar) {
		flex-wrap: wrap;
    }
    
    .p-button {
        margin-bottom: 0.25rem;
    }
}

</style>