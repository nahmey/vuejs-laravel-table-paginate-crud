<template>
    <div class="mb-4">
        <div class="card text-white bg-dark">
            <div class="card-header d-flex justify-content-between">
                <h5><i class="fas fa-filter" ref="icon_i_filters"></i> Filtrer par</h5>
                <template v-for="(filter, index) in fl">
                    <template v-if="filter.type == 'searchBar'">
                        <div class="w-50">
                            <input type="text" name="" placeholder="Search" class="form-control" v-model="search">
                        </div>
                    </template>
                </template>
                <a class="btn btn-light ml-4" href="#" v-on:click="resetFilter()"><i class="fas fa-ban"></i> Réinitialiser</a>
            </div>
            <div class="card-body d-flex">
                <div v-for="(filter, index) in fl" class="col-auto">
                    <template v-if="filter.type == 'select'">
                        <select ref="selectFilters" class="form-control" v-on:change="changeFilters($event.target.value, filter)" v-model="model[filter.name]">
                            <option value="0" selected="selected">TOUS</option>
                            <option v-for="subFilter in filter.data" :value="subFilter">{{subFilter.toUpperCase()}}</option>
                        </select>
                    </template>
                    <template v-else-if="filter.type == 'checkbox'">
                        <div class="d-flex flex-wrap">
                            <div v-for="(subFilter, index) in filter.data" class="custom-control custom-checkbox m-1">
                                <input type="checkbox" class="custom-control-input" 
                                :id="'checkbox' + index" 
                                :value="subFilter" 
                                v-on:change="changeFilters($event.target.value, filter)"
                                >
                                <label class="custom-control-label" :for="'checkbox' + index">{{subFilter.toUpperCase()}}</label>
                            </div>
                        </div>
                    </template>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    name:  'FiltreVue',
    props: {
        filters: Array,
    },
    data: function(){
        return{
            fl: this.filters,
            search: '',
            model: [],
            checkbox: [],
        }
        
    },
    mounted(){
        let self = this;
        let selected = {}
        this.filters.forEach(function(element, key){
            let name = element.name
            selected[name] = '0';
        });

        self.model = selected;

    },
    methods: {
        changeFilters: function(value, filter){
            if(filter.type == 'select'){
                this.model[filter.name] = value;
                this.$emit('changeFilters', {'value': value, 'name': filter.name, 'filter' : filter}); 
            }else if(filter.type == 'checkbox'){
                this.checkbox.push(value);
                this.model[filter.name] = this.checkbox;

                this.$emit('changeFilters', {'value': this.checkbox, 'name': filter.name,'filter' : filter});
            }
            
        },
        resetFilter: function(){
            let self = this;
            let selected = {};
            this.filters.forEach(function(element, key){
                let name = element.name
                selected[name] = '0';
            });
            self.model = selected;
            this.$emit('resetFilter', true);
        },
    },
    watch: {
        search: function(val){
           this.$emit('search', val); 
        }
    },
}
</script>
