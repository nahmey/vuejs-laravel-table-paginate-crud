Forked by tplasse (https://github.com/tplasse/vuejs-laravel-table-paginate-crud)
From nahmey (https://github.com/nahmey/vuejs-laravel-table-paginate-crud)
Fix sort by date. (didn't worked before)


# vuejs-laravel-table-paginate-crud

![alt text](https://julien-kennel.fr/images/git/table.PNG)

This CRUD Vue.js package offers an easy displaying Bootstrap/Laravel table tool.
This package also allows pagination and sorting with an easy configuration.

- [Dependencies](#dependencies)
- [Installation](#installation)
- [Example Usage](#example-usage)
- [Configuration](#configuration)
	- [Note](#note)
    - [Results](#results)
    - [Number Per Page](#number-per-page)
    - [Create Button](#create-button)
    - [Columns](#columns)

## Dependencies

* Laravel >=5.8. Required.
* Vue.js. Required.
* Bootstrap (CSS). Required.
* Moment JS Required.
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
import FilterVue from 'vuejs-laravel-table-pagination-crud/src/FilterVue.vue';


Vue.component('pagination-vue', PaginationVue);
Vue.component('custom-button-vue', CustomButtonVue);
Vue.component('modal-vue', ModalVue);
Vue.component('filter-vue', FilterVue);
```

## Example Usage
```html
<template>
    <pagination-vue v-if="load_data"  
    v-bind:columns="columns"
    v-bind:results="results"
    v-bind:numberPerPage="number_per_page"
    v-bind:createButton="create_button"
    v-bind:filters="filters"

    // Optionnal
    v-bind:sortDefault="{
        'key':'name',
        'order':'asc'
    }"

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
                filters: [],
        	}
        },
        method: {

        },
        mounted: function() {
            const self = this;
            axios.get('your url')
            .then(function (resp) {

                // Get result of request
                self.results = [{
                    'id': 1,
                    'name': 'Vader',
                    'firstname': 'Darth',
                    'age': '55',
                    'planete': 'Mustafar'
                },{
                    'id': 2,
                    'name' : 'Kenobi',
                    'firstname': 'Obi Wan',
                    'age': '60',
                    'planete': 'Tatoine'
                },{
                    'id': 3,
                    'name' : 'Bings',
                    'firstname': 'Jar Jar',
                    'age': '120',
                    'planete': 'Naboo'
                }];

                // Filters
                let planetes = ['Mustafar', 'Tatoine', 'Naboo'];
                self.filters = [
                {
                    data: planetes,
                    name: 'planete', // name of key in results
                    type: 'select', // (select or searchBar)
                },
                {
                    data: '',
                    name: '',
                    type: 'searchBar', // If searchBar, no data/name
                },
                ]

                // Number per page
                self.number_per_page = [10,20,50];

                self.create_button = {
                    button: true,
                    href: false,
                    text: 'Add User',
                    class_button: 'btn btn-primary text-white',
                    action: 'modal',
                    url: base_url + 'users',
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
                            },{
                                type: 'text',
                                label: 'First Name',
                                name: 'firstname',
                                id: 'firstname',
                                required: true,
                            }, {
                                type: 'text',
                                label: 'Age',
                                name: 'age',
                                id: 'age',
                                required: true,
                            }]
                        },
                        icon: 'fas fa-edit',
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

### Note

In the head of app :
```html
<meta name="csrf-token" content="{{ csrf_token() }}">
```

(optional)
```html
<script type="text/javascript">
    var base_url = "{!! url('/') !!}";
</script>
```

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
        title: 'Name', // Name of column
        key: 'name', // name of column in your database
        style: false, // add custom css
        class: false, // add boostrap class
		date: false, // set true if your data are date
		datetime: false, // set true if your data are datetime
    },
    {
        title: 'Age',
        key: 'age',
        style: false,
        class: false,
    },
    // Add button
    {
        title: 'Edit',  // Name of column
        key: false, // (true, false) - false if the column don't exist in your Database
        button: true, // Default : false - (true or false) for displaying button
        href: true, // Default : false - (true or false) True if <a href="">
        text: false,  // Default : false - (string) Text of button
        class_button: 'btn btn-primary', // Default : false - (string) bootstrap class button
        action: 'edit', // (create, edit, destroy) - Laravel CRUD action
        url: base_url + '/users', // Default : false - url which will be generated
        icon: 'fas fa-edit', // Default : false - Class of fontawesome icon
        style: 'width:10%', // Custom css style
        class: 'text-center', // Boostrap class

        // if you want modal
        modal: {
            modal: true,// Default : false - (true or false) for displaying modal
            name: 'edit_modal', // Default : false - name of modal
            modal_title: 'Edit', // Default : false - modal title
            action: 'edit', // (create, edit) - Laravel CRUD action
            url: base_url + '/users', // Default : false - url which will be generated
            method: 'POST', // Default : false - form method

            // You can add inputs for forms generation
            modal_inputs: [{
                type: 'text',
                label: 'Name',
                name: 'name',
                id: 'name',
                required: true,
            }]
        },

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
        alert: 'Are you sure ?', //Default: false - (false, string) You can add custom confirm alert
    },
];

```

You can declare default column with prop :

```html
v-bind:sortDefault="{
    'key':'your_key',
    'order':'asc or desc'
}"
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
    action: 'modal', // Default : false - (create, edit, destroy, url, modal)

    // Declare the type of CRUD action, url or modal if you wants that appear in a modal window
    url: base_url + 'users', // Default : false - url which will be generated
    icon: 'fas fa-plus-circle', // Default : false - Class of fontawesome icon

    // if you want modal
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
