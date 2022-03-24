<template>
    <div>
        <Dialog header="Create new group" v-model:visible="show_new_group_dialog"
            :modal="false" :draggable="false" ref="newgroupdialog">
            <span class="p-float-label">
                <InputText id="group_name_input" type="text" v-model="group_name" />
                <label for="group_name_input">Group name</label>
            </span>
            <Button label="Create" icon="pi pi-plus" class="p-button-success p-mr-2 p-col" @click="createNewGroup(group_name)" :disabled="!group_name"/>
        </Dialog>

        <Dialog header="Create new entry" v-model:visible="show_new_entry_dialog"
            :modal="false" :draggable="false" ref="newentrydialog">
            <span class="p-float-label">
                <InputText id="entry_namespace_input" type="text" v-model="entry_namespace" />
                <label for="entry_namespace_input">Namespace</label>
            </span>
            <span class="p-float-label">
                <InputText id="entry_uri_input" type="text" v-model="entry_iri" />
                <label for="entry_uri_input">URI</label>
            </span>
            <Button label="Create" icon="pi pi-plus" class="p-button-success p-mr-2 p-col" @click="createNewEntry(entry_namespace, entry_iri)"/>
        </Dialog>

        <div class="p-grid" style="padding-bottom: 5px;">
            <Dropdown class="p-col" v-model="selected_group" :options="prefixes_data" optionLabel="name" placeholder="Select a group" @change="changeDisplayedGroupData()"/>
            <Button label="Create New Group" icon="pi pi-plus" class="p-button-success p-mr-2 p-col" @click="openNewGroupDialog" />
            <Button label="Delete This Group" icon="pi pi-trash" class="p-button-danger p-col" @click="deleteSelectedGroup" :disabled="!selected_group"/>
            <Button label="Create New Entry" icon="pi pi-plus" class="p-button-success p-mr-2 p-col" @click="openNewEntryDialog" :disabled="!selected_group"/>
        </div>

        <DataTable :value="displayed_group_data" class="p-datatable-sm" responsiveLayout="scroll" :resizableColumns="true" columnResizeMode="fit" showGridlines stripedRows >
            <Column field="namespace" header="Namespace" :sortable="true"></Column>
            <Column field="uri" header="Uri" :sortable="true"></Column>
            <Column style="width: 241px;">
                <template #body="slotEntry">
                    <Button label="Insert" icon="pi pi-arrow-right" class="p-button-sm p-button-info tablebuttons" @click="$emit('insertentry', slotEntry.data)" />
                    <Button label="Edit" icon="pi pi-pencil" class="p-button-sm p-button-success tablebuttons" @click="editEntry(slotEntry.data)" />
                    <Button label="Delete" icon="pi pi-trash" class="p-button-sm p-button-warning tablebuttons" @click="deleteEntry(slotEntry.data)" />
                </template>
            </Column>
        </DataTable>

        <div>
            <Button label="Export" icon="pi pi-download" class="p-button-help p-col" @click="saveDataAsJson" style="float: right" />
            <FileUpload mode="basic" accept=".json" label="Import" chooseLabel="Import" class="p-mr-2 p-d-inline-block" :customUpload="true" @uploader="myUploader"/>
        </div>
    </div>
</template>

<script>
import 'primeflex/primeflex.css';
import DataTable from 'primevue/datatable';
import Column from 'primevue/column';
import Dropdown from 'primevue/dropdown';
import Button from 'primevue/button';
import FileUpload from 'primevue/fileupload';
import Dialog from 'primevue/dialog';
import InputText from 'primevue/inputtext';

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
            show_new_group_dialog: false,
            group_name: '',
            show_new_entry_dialog: false,
            entry_namespace: '',
            entry_iri: '',
            prefixes_data: [],
        }
    },
    components: {
        DataTable,
        Column,
        Dropdown,
        FileUpload,
        Button,
        Dialog,
        InputText,
    },
    created() {
        let retrived_object = window.localStorage.getItem('sparqleditor_prefixes')
        this.prefixes_data = JSON.parse(retrived_object) || [];
    },
    methods: {
        // displayed_group_data is displayed by data table, we want to show data from currently selected group
        changeDisplayedGroupData() {
            this.displayed_group_data = this.selected_group.data;
        },
        // open new group dialog
        openNewGroupDialog(){
            this.show_new_group_dialog = true
        },
        // open new group dialog
        openNewEntryDialog(){
            this.show_new_entry_dialog = true
        },
        // creates new group to which we can add iris
        createNewGroup(name){
            this.prefixes_data.push({"name": name, "data":[]});
            window.localStorage.setItem('sparqleditor_prefixes', JSON.stringify(this.prefixes_data));
            this.group_name = ''
            this.show_new_group_dialog = false
        },
        // deletes currently displayed group
        deleteSelectedGroup() {
            this.prefixes_data.splice(this.prefixes_data.indexOf(this.selected_group), 1);
            this.selected_group = null;
            window.localStorage.setItem('sparqleditor_prefixes', JSON.stringify(this.prefixes_data));
        },
        createNewEntry(namespace, uri) {
            this.prefixes_data[this.prefixes_data.indexOf(this.selected_group)].data.push({"namespace": namespace, "uri": uri});
            window.localStorage.setItem('sparqleditor_prefixes', JSON.stringify(this.prefixes_data));
            this.namespace = ''
            this.uri = ''
            this.show_new_entry_dialog = false
        },
        deleteEntry(event){
            this.prefixes_data[this.prefixes_data.indexOf(this.selected_group)].data.splice(this.prefixes_data[this.prefixes_data.indexOf(this.selected_group)].data.indexOf(event), 1);
            window.localStorage.setItem('sparqleditor_prefixes', JSON.stringify(this.prefixes_data));
        },

        //converts prefixes_data array into json and downloads it as file
        saveDataAsJson(){
            let dataStr = "data:text/json;charset=utf-8," + encodeURIComponent(JSON.stringify(this.prefixes_data));
            let downloadAnchorNode = document.createElement('a');
            downloadAnchorNode.setAttribute("href",     dataStr);
            downloadAnchorNode.setAttribute("download", "prefixes_data.json");
            document.body.appendChild(downloadAnchorNode); // required for firefox
            downloadAnchorNode.click();
            downloadAnchorNode.remove();
        },
        //reads json file and rewrites prefixes_data
        myUploader(event) {
            let fr = new FileReader();
            //view model
            let vm = this;

            fr.onload = function(e) {
                let result = JSON.parse(e.target.result);
                vm.prefixes_data = result;
            }
            fr.readAsText(event.files[0]);
            window.localStorage.setItem('sparqleditor_prefixes', JSON.stringify(this.prefixes_data));
            //TODO reset fileuploader
        },
        editEntry(entry)
        {
            console.log(entry);
            window.localStorage.setItem('sparqleditor_prefixes', JSON.stringify(this.prefixes_data));
        },
        insertEntry(entry)
        {
            console.log("called in PrefixImporter");
            this.$emit('insertEntryIntoCode', entry);
        },
    }
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