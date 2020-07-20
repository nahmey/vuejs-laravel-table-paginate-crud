# vuejs-laravel-table-paginate-crud

![alt text](http://url/to/img.png)

This CRUD Vue package offers an easy way of displaying Bootstrap-styled table with data with Laravel.
This package also allows pagination and sorting with simple configuration.

- [Dependencies](#dependencies)
- [Installation](#installation)
- [Example Usage](#example-usage)
- [Configuration](#configuration)
    - [Results](#results)
    - [Number Per Page](#number-per-page)
    - [Create Button](#create-button)
    - [Columns](#columns)

## Dependencies

* Laravel >=5.8. Required.
* Vue.js. Required.
* Bootstrap (CSS). Required.
* Vue-js-modal. Required. (https://www.npmjs.com/package/vue-js-modal)
* Vue-js Dialog. Required. (https://www.npmjs.com/package/vuejs-dialog)
* Fontawesome. Optional. (https://fontawesome.com/)

## Installing

Install with npm:
```bash
npm i vuejs-laravel-table-pagination-crud
```

Import globally in app.js:

```javascript
/*
 * Import vuejs dialog (confirm, prompt...)
 */
import VuejsDialog from 'vuejs-dialog';
import VuejsDialogMixin from 'vuejs-dialog/dist/vuejs-dialog-mixin.min.js'; // only needed in custom components
import 'vuejs-dialog/dist/vuejs-dialog.min.css';
window.Vue.use(VuejsDialog);

/*
 * Import vuejs modal
 */
import VModal from 'vue-js-modal';
window.Vue.use(VModal);

/*
 * Import vuejs-laravel-table-pagination-crud
 */
import PaginationVue from 'vuejs-laravel-table-pagination-crud/src/PaginationVue.vue';
import CustomButtonVue from 'vuejs-laravel-table-pagination-crud/src/CustomButtonVue.vue';
import ModalVue from 'vuejs-laravel-table-pagination-crud/src/ModalVue.vue';


Vue.component('pagination-vue', PaginationVue);
Vue.component('custom-button-vue', CustomButtonVue);
Vue.component('modal-vue', ModalVue);


// import VueGoodTablePlugin from 'vue-good-table';

// // import the styles 
// import 'vue-good-table/dist/vue-good-table.css'

// Vue.use(VueGoodTablePlugin);
```

## Example Usage
```html
<template>
	<pagination-vue v-if="load_data"  
    v-bind:columns="columns" 
    v-bind:results="results" 
    v-bind:numberPerPage="number_per_page" 
    v-bind:createButton="create_button"
    ></pagination-vue>
    
    <div v-else class="text-center">
        <i class="fas fa-circle-notch fa-spin"></i> Loading...
    </div>
</template>


<script>
    export default {
        data: function(){
        	return {
                load_data: false,
        		columns: [],
	            data: [],
	            results: [],
                number_per_page: [],
        	}
        },
        method: {
            test: function(val){
                console.log(val);
            }
        },
        mounted: function() {
            const self = this;
            axios.get(base_url + '/users/load_data')
            .then(function (resp) {
                self.number_per_page = [10,20,50];

                self.create_button = {
                    button: true,
                    href: false,
                    text: 'Ajouter Type',
                    class_button: 'btn btn-primary text-white',
                    action: 'modal',
                    // url: base_url + 'users',
                    icon: 'fas fa-plus-circle',
                    modal: {
                        modal: true,
                        name: 'create_modal',
                        modal_title: 'Add User',
                        action: 'create',
                        url: base_url + '/users',
                        method: 'POST',
                        modal_inputs: [{
                            type: 'text',
                            label: 'Name',
                            name: 'name',
                            id: 'name',
                            required: true,
                        }]
                    },
                    
                }

                self.columns = [
                    {
                        title: 'Name',
                        key: 'name',
                        style: false,
                        class: false,
                    },
                    {
                        title: 'Edit',
                        key: false,
                        button: true,
                        href: true,
                        text: false,
                        class_button: 'btn btn-primary',
                        action: 'edit',
                        url: base_url + '/users',
                        modal: {
                            modal: true,
                            name: 'edit_modal',
                            modal_title: 'Edit',
                            action: 'edit',
                            url: base_url + '/users',
                            method: 'PUT',
                            modal_inputs: [{
                                type: 'text',
                                label: 'Name',
                                name: 'name',
                                id: 'name',
                                required: true,
                            }]
                        },
                        icon: 'fas fa-edit'
                        style: 'width:10%',
                        class: 'text-center',
                    },
                    {
                        title: 'Delete',
                        key: false,
                        button: true,
                        href: false,
                        text: false,
                        class_button: 'btn btn-danger',
                        action: 'destroy',
                        modal: {
                            modal: false,
                            name: false,
                        },
                        url: base_url + '/users',
                        icon: 'fas fa-trash-alt',
                        style: 'width:10%',
                        class: 'text-center',
                        alert: 'Are you sure ?',
                    },
                ];

                self.results = resp.data['users'];
                self.load_data = true;
            })
            .catch(function (resp) {
                alert("Error");
            });
        },
    };
</script>
```

## Configuration

### Results 
<!-- `Array` -->
self.results = resp.data['your_data'];

### Number Per Page 
Number of element displaying

self.number_per_page = [10,20,50];

### Columns



```javascript
self.columns = [
    {
        title: 'Name',
        key: 'name',
        style: false,
        class: false,
    },
    {
        title: 'Age',
        key: 'age',
        style: false,
        class: false,
    },
    {
        title: 'Edit',  // Name of column
        key: false, // pas de clé si il n'a pas besoin d'être sort by
        button: true, // si true on affiche bouton, si false on affiche rien
        href: true, // Si true, on fait un a href et pas un bouton
        text: false,  // si != false on affiche le texte du bouton
        class_button: 'btn btn-primary', // si != false class du bouton bootstrap
        action: 'edit', // si != false (edit, delete, url, modal)
        url: base_url + '/type_lynx',
        modal: {
            modal: true,
            name: 'edit_modal',
            modal_title: 'Modifier',
            action: 'edit', //Create  ou edit
            url: base_url + '/type_lynx', // Vers le controller
            method: 'PUT', // POST, PUT, DELETE
            modal_inputs: [{
                type: 'text',
                label: 'Nom',
                name: 'nom',
                id: 'nom',
                required: true,
            }]
        },
        icon: 'fas fa-edit', // si != false on affiche l'icon
        style: 'width:10%',
        class: 'text-center',
    },
    {
        title: 'Supprimer',  // Titre de la colonne
        key: false, // pas de clé si il n'a pas besoin d'être sort by
        button: true, // si true on affiche bouton, si false on affiche rien
        href: false, // Si true, on fait un a href et pas un bouton
        text: false,  // si != false on affiche le texte du bouton
        class_button: 'btn btn-danger', // si != false class du bouton bootstrap
        action: 'destroy', // si != false, soit lien si href, soit lien vers l'appli
        modal: {
            modal: false,
            name: false,
        },
        url: base_url + '/type_lynx',
        icon: 'fas fa-trash-alt', // si != false on affiche l'icon
        style: 'width:10%',
        class: 'text-center',
        alert: 'Êtes-vous bien sûr de vouloir faire cela ? La suppression est irréversible !',
    },
];

```




### Create Button
<!-- `Array` -->

If you want a create button, just declare self.create_button in mounted() function :

```javascript
self.create_button = {
    button: true, // Default : false - (true or false) for displaying button
    href: false, // Default : false - (true or false) True if <a href="">
    text: 'Add Users',  // Default : false - (string) Text of button 
    class_button: 'btn btn-primary text-white', // Default : false - (string) bootstrap class button
    action: 'modal', // Default : false - (create, edit, delete, url, modal)

    // Declare the type of CRUD action, url or modal if you wants that appear in a modal window
    url: base_url + 'users', // Default : false - url which will be generated
    icon: 'fas fa-plus-circle', // Default : false - Class of fontawesome icon

    //  So if you declare modal in action :
    modal: {
        modal: true, // Default : false - (true or false) for displaying modal
        name: 'create_modal', // Default : false - name of modal
        modal_title: 'Add users', // Default : false - modal title
        action: 'create', // (create, edit) - Laravel CRUD action
        url: base_url + '/users', // Default : false - url which will be generated
        method: 'POST', // Default : false - form method

        // You can add inputs for forms generation
        modal_inputs: [{
            type: 'text', // (string) - Type of input
            label: 'Name', // (string) - Label of input
            name: 'name', // (string) - Name of input
            id: 'name', // (string) - Id of input
            required: true, // Default : false - if input must be required
        }]
    },
}
```