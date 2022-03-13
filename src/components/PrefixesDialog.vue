<template>
    TEST
    <!-- <div class="p-grid" style="padding-bottom: 5px;">
        <Dropdown class="p-col" v-model="selected_group" :options="prefixes_data" optionLabel="name" placeholder="Select a group" @change="changeDisplayedGroupData()"/>
        <Button label="Create New Group" icon="pi pi-plus" class="p-button-success p-mr-2 p-col" @click="createNewGroup" />
        <Button label="Delete This Group" icon="pi pi-trash" class="p-button-danger p-col" @click="deleteSelectedGroup" :disabled="!selected_group"/>
        <Button label="Create New Entry" icon="pi pi-plus" class="p-button-success p-mr-2 p-col" @click="createNewEntry" :disabled="!selected_group"/>
        <Button label="Export" icon="pi pi-upload" class="p-button-help p-col" @click="saveDataAsJson" style="float: right" />
        <FileUpload mode="basic" accept=".json" label="Import" chooseLabel="Import" class="p-mr-2 p-d-inline-block" :customUpload="true" @uploader="myUploader"/>
    </div>

    <DataTable :value="displayed_group_data" class="p-datatable-sm" responsiveLayout="scroll" :resizableColumns="true" columnResizeMode="fit" showGridlines stripedRows >
        <Column field="namespace" header="Namespace" :sortable="true"></Column>
        <Column field="iri" header="IRI" :sortable="true"></Column>
        <Column style="width: 241px;">
            <template #body="slotEntry">
                <Button label="Insert" icon="pi pi-arrow-right" class="p-button-sm p-button-info tablebuttons" @click="$emit('insertentry', slotEntry.data)" />
                <Button label="Edit" icon="pi pi-pencil" class="p-button-sm p-button-success tablebuttons" @click="editEntry(slotEntry.data)" />
                <Button label="Delete" icon="pi pi-trash" class="p-button-sm p-button-warning tablebuttons" @click="deleteEntry(slotEntry.data)" />
            </template>
        </Column>
    </DataTable> -->
</template>

<script>
import 'primeflex/primeflex.css';
// import DataTable from 'primevue/datatable';
// import Column from 'primevue/column';
// import Dropdown from 'primevue/dropdown';
// import Button from 'primevue/button';
// import FileUpload from 'primevue/fileupload';

//TODO editing entry
//TODO dialog for new group name
//TODO dialog for new entry namespace, iri

export default {
    name: 'PrefixesDialog',
    emits: ['insertentry',],
    data() {
        return {
            selected_group: null,
            displayed_group_data: null,
            prefixes_data: [],
        }
    },
    components: {
        // DataTable,
        // Column,
        // Dropdown,
        // FileUpload,
        // Button,
    },
    // created() {
    //     try{
    //         let retrived_object = window.localStorage.getItem('sparqleditor_prefixes')
    //         this.prefixes_data = JSON.parse(retrived_object);
    //     }
    //     catch(error){
    //         this.prefixes_data = []
    //     }
    // },
    // methods: {
    //     // displayed_group_data is displayed by data table, we want to show data from currently selected group
    //     changeDisplayedGroupData() {
    //         this.displayed_group_data = this.selected_group.data;
    //     },
    //     // creates new group to which we can add iris
    //     createNewGroup(){
    //         this.prefixes_data.push({"name": "NewGroup", "data":[]});
    //         window.localStorage.setItem('sparqleditor_prefixes', JSON.stringify(this.prefixes_data));
    //     },
    //     // deletes currently displayed group
    //     deleteSelectedGroup() {
    //         this.prefixes_data.splice(this.prefixes_data.indexOf(this.selected_group), 1);
    //         this.selected_group = null;
    //         window.localStorage.setItem('sparqleditor_prefixes', JSON.stringify(this.prefixes_data));
    //     },
    //     createNewEntry() {
    //         this.prefixes_data[this.prefixes_data.indexOf(this.selected_group)].data.push({"namespace": "event.inputNamespace", "iri": "event.inputIri"});
    //         window.localStorage.setItem('sparqleditor_prefixes', JSON.stringify(this.prefixes_data));
    //     },
    //     deleteEntry(event){
    //         this.prefixes_data[this.prefixes_data.indexOf(this.selected_group)].data.splice(this.prefixes_data[this.prefixes_data.indexOf(this.selected_group)].data.indexOf(event), 1);
    //         window.localStorage.setItem('sparqleditor_prefixes', JSON.stringify(this.prefixes_data));
    //     },

    //     //converts prefixes_data array into json and downloads it as file
    //     saveDataAsJson(){
    //         let dataStr = "data:text/json;charset=utf-8," + encodeURIComponent(JSON.stringify(this.prefixes_data));
    //         let downloadAnchorNode = document.createElement('a');
    //         downloadAnchorNode.setAttribute("href",     dataStr);
    //         downloadAnchorNode.setAttribute("download", "prefixes_data.json");
    //         document.body.appendChild(downloadAnchorNode); // required for firefox
    //         downloadAnchorNode.click();
    //         downloadAnchorNode.remove();
    //     },
    //     //reads json file and rewrites prefixes_data
    //     myUploader(event) {
    //         let fr = new FileReader();
    //         //view model
    //         let vm = this;

    //         fr.onload = function(e) {
    //             let result = JSON.parse(e.target.result);
    //             vm.prefixes_data = result;
    //         }
    //         fr.readAsText(event.files[0]);
    //         window.localStorage.setItem('sparqleditor_prefixes', JSON.stringify(this.prefixes_data));
    //         //TODO reset fileuploader
    //     },
    //     editEntry(entry)
    //     {
    //         console.log(entry);
    //         window.localStorage.setItem('sparqleditor_prefixes', JSON.stringify(this.prefixes_data));
    //     },
    //     insertEntry(entry)
    //     {
    //         console.log("called in PrefixImporter");
    //         this.$emit('insertEntryIntoCode', entry);
    //     },
    // }
}


</script>
<style>
.p-fileupload.p-fileupload-basic.p-component {
    display: inline-flex;
    float: right;
}
span.p-button.p-component.p-fileupload-choose.p-mr-2.p-d-block {
    top: 2px;
}
.tablebuttons {
    height: 1.5rem;
}
</style>