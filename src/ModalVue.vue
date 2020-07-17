<template>
    <modal v-bind:name="na" v-bind:click-to-close="false" height="auto" scrollable>
        <div class="card col-12 p-0 h-100">
            <form v-bind:method="meth" v-on:submit.prevent="createEdit()" v-bind:action="ur">
                <div class="card-header clearfix">
                    <div class="float-right">
                        <button @click="hideModal()" type="button" class="close">
                            <span aria-hidden="true">&times;</span>
                        </button>
                    </div>
                    <h5><i class="fas fa-pen"></i> {{title}}</h5>
                </div>
                <div class="card-body">
                    <div class="alert alert-danger" v-if="err.length">
                        <a class="btn btn-small text-danger close" v-on:click="err = []"><i class="fas fa-times"></i></a>
                        <ul>
                            <li v-for="error in err">{{ error }}</li>
                        </ul>
                    </div>
                    <p v-for="input, key in inputs">
                        <label v-bind:for="input.id">{{input.label}}</label>
                        <template v-if="input.required == true">
                            <input v-bind:id="input.id" v-bind:type="input.type" v-bind:name="input.name" v-model="variables[key]" class="form-control" required="required">
                        </template>
                        <template v-else>
                            <input v-bind:id="input.id" v-bind:type="input.type" v-bind:name="input.name" v-model="variables[key]" class="form-control">
                        </template>
                    </p>      
                </div>

                <div class="modal-footer">
                    <button class="btn btn-dark" type="submit">
                        <i class="fas fa-save"></i> Enregistrer
                    </button>
                    <!-- <button @click="$modal.hide('edit-modal')" type="button" class="btn btn-light" data-dismiss="modal"><i class="fas fa-times-circle"></i>Quitter</button> -->
                    <button @click="hideModal()" type="button" class="btn btn-light" data-dismiss="modal"><i class="fas fa-times-circle"></i>Quitter</button>
                </div>
            </form>
        </div>
    </modal>
</template>



<script>
    export default {
        name:  'ModalVue',
        props: {
            name: false,
            submit: false,
            errors: false,
            modalTitle: false,
            modalInputs: false,
            action: false,
            url: false,
            method: false,
            identifiant: false,

        },
        data: function(){
            return{
                na: this.name,
                sub: this.submit,
                title: this.modalTitle,
                inputs : this.modalInputs,
                act: this.action,
                ur: this.url,
                meth: this.method,
                id: this.identifiant,
                err: [],
                csrf: document.querySelector('meta[name="csrf-token"]').getAttribute('content'),
                variables: [],
            }

            console.log(this.na);
        },
        watch: { 
            name: function(newVal, oldVal) {
                this.na = newVal;
            },
            submit: function(newVal, oldVal) {
                this.sub = newVal;
            },
            modalTitle: function(newVal, oldVal) {
                this.title = newVal;
            },
            modalInputs: function(newVal, oldVal) {
                this.inputs = newVal;
            },
            action: function(newVal, oldVal) {
                this.act = newVal;
            },
            url: function(newVal, oldVal) {
                this.ur = newVal;
            },
            method: function(newVal, oldVal) {
                this.meth = newVal;
            },
            identifiant: function(newVal, oldVal) {
                this.id = newVal;
            },
        },
        methods: {
            showModal: function(modal, objet){
                // this.na = value.name;
                this.title = modal.modalTitle;
                this.inputs = modal.modalInputs;
                this.act = modal.action;
                this.ur = modal.url;
                this.meth = modal.method;
                this.id = modal.identifiant;

                const self = this;
                if(self.inputs != undefined){
                    self.inputs.forEach(function(element, key){
                        if(self.act == 'edit'){
                            if(objet[0][element.name] != undefined) self.variables[key] = objet[0][element.name]; 
                            else self.variables[key] = "";
                        }else self.variables[key] = "";
                    }) 
                }

                console.log(this.meth)

                this.$modal.show(this.na);
            },
            hideModal(){
                this.$modal.hide(this.na);
            },
            createEdit: function(){
                if(this.act == 'create'){
                    this.create();
                }else if(this.act == 'edit'){
                    this.edit();
                }
            },
            create: function(e){
                let self = this;
                self.err = [];

                /* 
                 * Form validation && generation
                 */
                let formData = new FormData();

                self.variables.forEach(function(element, key){
                    if(self.inputs[key].required == true){
                        if(element.length == 0 || element == null) self.err.push('Le champ '+self.inputs[key].name+' est obligatoire');
                    }
                    
                    formData.append(self.inputs[key].name, element);
                });

                if (this.err.length) return false;

                axios.post(self.ur, formData, { 
                    _method: self.meth,
                })
                .then(function (response) { 
                    if(response.data.error == true){
                        var values = Object.keys(response.data.message).map(function(e) {
                            return self.err = response.data.message[e];
                        });
                        return false;
                    }else{
                        self.$emit('createResult', response.data);
                        self.err = [];
                        self.variables = [];
                        self.$modal.hide(self.na);
                    }
                })
                .catch(function (error) { console.log(error); });
            },
            edit(){
                let self = this;
                self.err = [];

                /* 
                 * Form validation && generation
                 */
                let formData = new FormData();

                self.variables.forEach(function(element, key){
                    if(self.inputs[key].required == true){
                        if(element.length == 0 || element == null) self.err.push('Le champ '+self.inputs[key].name+' est obligatoire');
                    }
                    
                    formData.append(self.inputs[key].name, element);
                });

                formData.append('_method', self.meth);

                if(this.err.length) return false;

                axios.post(self.ur, formData)
                .then(function (response) { 
                    if(response.data.error == true){
                        var values = Object.keys(response.data.message).map(function(e) {
                            return self.err = response.data.message[e];
                        });
                        return false;
                    }else{
                        self.$emit('editResult', response.data);
                        self.err = [];
                        self.variables = [];
                        self.$modal.hide(self.na);
                    }
                })
                .catch(function (error) { console.log(error); });
            }
        },
        // watch: {
        //     variables: function(val) {
        //         variables = val;
        //     },
        // }
    }
</script>