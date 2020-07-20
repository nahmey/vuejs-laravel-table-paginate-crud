# vuejs-laravel-table-paginate-crud

# Dependencies

* Laravel >=5.8. Required.
* Vue.js. Required.
* Bootstrap (CSS). Required.
* Vue-js-modal. Required. (https://www.npmjs.com/package/vue-js-modal)
* Vue-js Dialog. Required. (https://www.npmjs.com/package/vuejs-dialog)

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

Import into your component
```js
import { VueGoodTable } from 'vue-good-table';

// add to component
components: {
  VueGoodTable,
}
```

Import into your component using Typescript
```typescript
// add to component
components: {
  'vue-good-table': require('vue-good-table').VueGoodTable,
}