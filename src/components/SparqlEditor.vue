<template>
	<div>
		<Menubar :model="editorBarItems" class="editor_bar"/>
		<Dialog header="Prefixes manager" v-model:visible="displayPrefixes"
			:style="{width: '75vw'}" :maximizable="true"
			:modal="false" :draggable="false" ref="prefixesdialog"
			@show="showDialogPrefixes($event)">
			<PrefixesDialog @insertentry="insertEntryIntoCode"/>
		</Dialog>
		<Dialog header="Preferences" v-model:visible="displayPreferences"
			:style="{width: '75vw'}" :maximizable="true"
			:modal="false" :draggable="false" ref="preferencesdialog"
			@show="showDialogPreferences($event)">
			<PreferencesDialog @changeTheme="changeTheme"/>
		</Dialog>
		<textarea v-model="content" id="editor"></textarea>
		<div class="editor_footer"> Ln {{this.currentLineNumber}}, Col {{this.currentColNumber}}</div>
	</div>
</template>

<script>
import * as CodeMirror from 'codemirror';
import 'codemirror/lib/codemirror.css';
import 'codemirror/theme/midnight.css';
import 'codemirror/mode/sparql/sparql.js';
import 'codemirror/addon/hint/show-hint.css';
import 'codemirror/addon/hint/show-hint.js';
import 'codemirror/addon/hint/anyword-hint.js';
import 'codemirror/addon/edit/matchbrackets.js';
import 'codemirror/addon/edit/closebrackets.js';

import Menubar from 'primevue/menubar';
import Dialog from 'primevue/dialog';
import PrefixesDialog from '@/components/PrefixesDialog.vue'
import PreferencesDialog from '@/components/PreferencesDialog.vue'
import {PrimeIcons} from 'primevue/api';

export default {
  name: 'SparqlEditor',
  props: {
    codeValue: String,
  },
  components:{
		Menubar,
		Dialog,
		PrefixesDialog,
		PreferencesDialog,
  },
  data(){
		return{
			theme: 'midnight',
			currentLineNumber: "",
			currentColNumber: "",
			content: "PREFIX a: <http://www.w3.org/2000/10/annotation-ns#>\nPREFIX dc: <http://purl.org/dc/elements/1.1/>\nPREFIX foaf: <http://xmlns.com/foaf/0.1/>\nPREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>\n # Comment!\n SELECT ?given ?family\nWHERE {\n{\n?annot a:annotates <http://www.w3.org/TR/rdf-sparql-query/> .\n?annot dc:creator ?c .\nOPTIONAL {?c foaf:givenName ?given ;\nfoaf:familyName ?family }\n} UNION {\n?c !foaf:knows/foaf:knows? ?thing.\n?thing rdfs\n} MINUS {\n?thing rdfs:label \"剛柔流\"@jp\n}\nFILTER isBlank(?c)\n}\n",
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
		showDialogPrefixes: function (e) {
			setTimeout(() => {
				const element = this.$refs.prefixesdialog;
				if(element.maximized == false)
				{
						element.maximize(e);
				}
			}, 0);
		},
		showDialogPreferences: function(e){
			setTimeout(() => {
				const element = this.$refs.preferencesdialog;
				if(element.maximized == false)
				{
						element.maximize(e);
				}
			}, 0);
		},
		addTab(){

		},
		changeTheme(){

		},
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