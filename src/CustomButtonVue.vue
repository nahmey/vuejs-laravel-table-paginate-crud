<template>
	<div>
		<template v-if="hr == false && act != 'destroy' && mod.modal == false && act != 'vue-router'">
			<button :class="cl != false ? cl : ''">
				<template v-if="ic != false">
					<i :class="ic"></i>
				</template>
				<template v-if="text != false">
					{{txt}}
				</template>
			</button>
		</template>
		<template v-else-if="hr == true && act != 'destroy' && mod.modal == false && act != 'vue-router'">
			<a :class="cl != false ? cl : ''" :href="generateLink()">
				<template v-if="ic != false">
					<i :class="ic"></i>
				</template>
				<template v-if="text != false">
					{{txt}}
				</template>
			</a>
		</template>
		<template v-else-if="ur != false && act == 'vue-router'">
			<router-link :class="cl != false ? cl : ''" :to="generateLink()">
				<template v-if="ic != false">
					<i :class="ic"></i>
				</template>
				<template v-if="text != false">
					{{txt}}
				</template>
			</router-link>
		</template>
		<template v-else-if="act == 'destroy' && mod.modal == false && act != 'vue-router'">
			<form method="POST" v-bind:action="generateLink()" @submit.prevent="checkIfAlert()" v-bind:id="'destroy_form_' + id">
	            <input type="hidden" name="_token" :value="csrf">
	            <input type="hidden" name="_method" value="DELETE">
	            <button :class="cl != false ? cl : ''" type="submit">
	            	<template v-if="ic != false">
						<i :class="ic"></i>
					</template>
	            	<template v-if="text != false">
						{{txt}}
					</template>
	            </button>                  
	        </form>
		</template>
		<template v-else-if="mod.modal == true && act != 'vue-router'">
			<button :class="cl != false ? cl : ''" @click="modalInfo()">
				<template v-if="ic != false">
					<i :class="ic"></i>
				</template>
				<template v-if="text != false">
					{{txt}}
				</template>
			</button>
		</template>
	</div>
</template>


<script>
	export default {
    	name:  'ButtonVue',
    	props: {
		    classButton: false,
		    text: false,
            action: false,
            icon: false,
            identifiant:false,
            href: false,
            url: false,
            alert: false,
            modal: false,
		},
		data: function(){
            return{
            	cl: this.classButton,
            	txt: this.text,
            	act: this.action,
            	ic: this.icon,
            	id: this.identifiant,
            	hr: this.href,
            	ur: this.url,
            	al: this.alert,
            	mod: this.modal,
            	form_validation: this.alert != false ? false : true, 
            	csrf: document.querySelector('meta[name="csrf-token"]').getAttribute('content'),
            }
        },
        methods: {
        	create(event){
        		this.$emit('createResult', event);
        	},
        	generateLink: function(){
        		if(this.action == 'edit' && this.mod.modal == false){
        			return this.url + '/' + this.identifiant + '/edit';
        		}else if(this.action == 'destroy'){
        			if(this.url != false && this.identifiant != false){
        				return this.url + '/' + this.identifiant;
        			}
        		}else if(this.action == 'url'){
        			return this.url;
        		}else if(this.action == 'vue-router'){
        			let param = this.identifiant != undefined ? '/' + this.identifiant : '';
        			return this.url + param;
        		}
        	},
        	modalInfo(){
        		if(this.mod != false && this.mod.nom != false){
        			if(this.act == 'edit'){
        				this.mod.identifiant = this.id;
        				this.mod.url = this.url + '/' + this.identifiant;
        			}
        			this.$emit('modalInfo', this.mod);
        		}
        		else alert("Vous n'avez pas renseigné le nom de la fenêtre modal");
            },
        	checkIfAlert: function(e){
        		var self = this;
        		let message = 'Êtes-vous bien sûr de vouloir faire cela ? La suppression est irréversible.';
        		let options = {
				    html: false, // set to true if your message contains HTML tags. eg: "Delete <b>Foo</b> ?"
				    loader: false, // set to true if you want the dailog to show a loader after click on "proceed"
				    reverse: false, // switch the button positions (left to right, and vise versa)
				    okText: 'Confirmer',
				    cancelText: 'Fermer',
				    animation: 'zoom', // Available: "zoom", "bounce", "fade"
				    type: 'basic', // coming soon: 'soft', 'hard'
				    verification: 'continue', // for hard confirm, user will be prompted to type this to enable the proceed button
				    verificationHelp: 'Type "[+:verification]" below to confirm', // Verification help text. [+:verification] will be matched with 'options.verification' (i.e 'Type "continue" below to confirm')
				    clicksCount: 3, // for soft confirm, user will be asked to click on "proceed" btn 3 times before actually proceeding
				    backdropClose: false, // set to true to close the dialog when clicking outside of the dialog window, i.e. click landing on the mask
				    customClass: '' // Custom class to be injected into the parent node for the current dialog instance
				};
        		if(this.alert != false){
        			this.$dialog
            		.confirm(message, options)
              		.then(function(dialog) {
		                let url = document.getElementById('destroy_form_'+self.id).getAttribute("action");
		                
		                axios.post(url, { 
		                    _method: 'DELETE',
		                })
		                .then(function (response) { 
		                    if(response.data.error == true){
		                        var values = Object.keys(response.data.message).map(function(e) {
		                            return self.errors = response.data.message[e];
		                        });
		                        return false;
		                    }else{
		                        self.$emit('destroyResult', self.id);
		                        self.id = false;
		                    }
		                })
		                .catch(function (error) { console.log(error); });
              		})
              		.catch(function() {
                		console.log('Clicked on cancel');
              		});
        		}
        	}
        }
	}
</script>