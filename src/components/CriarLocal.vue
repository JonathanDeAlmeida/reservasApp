<template>
    <div class="container mt-65">

        <div v-if="alert.status" :class="'alert-general ' + alert.type">
            <div :class="'border-alert ' + alert.type">
                <span>{{alert.title}}</span>
            </div>
            <div>
                <span>{{alert.message}}</span>
            </div>
        </div>

        <div class="row">
            <div class="col-md-12">
                
                <template v-if="$route.params.id">
                    <div style="margin-bottom: 50px">
                        <vuedropzone
                            v-on:vdropzone-success="addedDropZoneProfileFile"
                            v-on:vdropzone-removed-file="removedDropZoneProfileFile"
                            :destroyDropzone="false"
                            ref="myVueDropzone"
                            id="myVueDropzone"
                            :options="dropzoneOptions">
                        </vuedropzone>
                    </div>
                </template>       

                <ValidationObserver v-slot="{ handleSubmit }">
                    <form @submit.prevent="handleSubmit(formSubmit)">
                        
                        <div class="row">
                            <div class="col-md-2 mb-25">
                                <select class="select-line" v-model="form.intent">
                                    <option value="rent">Alugar</option>
                                    <option value="sell">Vender</option>
                                </select>
                            </div>
                            <div class="col-md-2 mb-25">
                                <select class="select-line" v-model="form.condition">
                                    <option value="residencial">Residencial</option>
                                    <option value="comercial">Comercial</option>
                                </select>
                            </div>
                            <div class="col-md-2 mb-25">
                                <select class="select-line" v-model="form.type">
                                    <option v-for="(type, index) of $store.state.types" :key="index">
                                        {{type.value}}
                                    </option>
                                </select>
                            </div>
                            <div class="col-md-2 mb-25">
                                <label class="label-line">Contato</label>
                                <ValidationProvider rules="required" v-slot="{ errors }">
                                    <input v-model="form.phone" class="input-line">
                                    <span class="form-error">{{ errors[0] }}</span>
                                </ValidationProvider>
                            </div>
                            <template v-if="form.intent === 'rent'">
                                <div class="col-md-2 mb-25">
                                    <label class="label-line">Valor do Aluguel</label>
                                    <ValidationProvider rules="integer" v-slot="{ errors }">
                                        <input v-model="form.rent_value" class="input-line">
                                        <span class="form-error">{{ errors[0] }}</span>
                                    </ValidationProvider>
                                </div>
                            </template>
                            <template v-else>
                                <div class="col-md-2 mb-25">
                                    <label class="label-line">Valor de Venda</label>
                                    <ValidationProvider rules="integer" v-slot="{ errors }">
                                        <input v-model="form.sale_value" class="input-line">
                                        <span class="form-error">{{ errors[0] }}</span>
                                    </ValidationProvider>
                                </div>
                            </template>
                        </div>
                    
                        <div class="row">
                            <div class="col-md-2 mb-25">         
                                <label class="label-line">Área útil (m²) (Opcional)</label>                               
                                <ValidationProvider rules="required|integer" v-slot="{ errors }">
                                    <input v-model="form.area" class="input-line">
                                    <span class="form-error">{{ errors[0] }}</span>
                                </ValidationProvider>  
                            </div>
                            <div class="col-md-2 mb-25">
                                <label class="label-line">Banheiros (Opcional)</label>
                                <ValidationProvider rules="required|integer" v-slot="{ errors }">
                                    <input v-model="form.bathrooms" class="input-line">
                                    <span class="form-error">{{ errors[0] }}</span>
                                </ValidationProvider>
                            </div>
                            <template>
                                <div class="col-md-2 mb-25">
                                    <label class="label-line">Quartos (Opcional)</label>
                                    <ValidationProvider rules="integer" v-slot="{ errors }">
                                        <input v-model="form.rooms" class="input-line">
                                        <span class="form-error">{{ errors[0] }}</span>
                                    </ValidationProvider>
                                </div>
                                <div class="col-md-2 mb-25">
                                    <label class="label-line">Suítes (Opcional)</label>
                                    <ValidationProvider rules="integer" v-slot="{ errors }">
                                        <input v-model="form.suites" class="input-line">
                                        <span class="form-error">{{ errors[0] }}</span>
                                    </ValidationProvider>
                                </div>
                                <div class="col-md-3 mb-25">
                                    <label class="label-line">Valor do Condomínio (Opcional)</label>
                                    <ValidationProvider rules="integer" v-slot="{ errors }">
                                        <input v-model="form.condominium_value" class="input-line">
                                        <span class="form-error">{{ errors[0] }}</span>
                                    </ValidationProvider> 
                                </div>
                            </template>
                        </div>
                            
                        <div class="row">
                            <div class="col-md-2 mb-25">
                                <label class="label-line">IPTU (Opcional)</label>
                                <ValidationProvider rules="integer" v-slot="{ errors }">
                                    <input v-model="form.iptu" class="input-line">
                                    <span class="form-error">{{ errors[0] }}</span>
                                </ValidationProvider>
                            </div>
                            <div class="col-md-2 mb-25">
                                <label class="label-line">Vagas (Opcional)</label>
                                <ValidationProvider rules="integer" v-slot="{ errors }">
                                    <input v-model="form.vacancies" class="input-line">
                                    <span class="form-error">{{ errors[0] }}</span>
                                </ValidationProvider>
                            </div>
                            <div class="col-md-2 mb-25">
                                <label class="label-line">Número</label>
                                <ValidationProvider rules="required|integer" v-slot="{ errors }">
                                    <input v-model="form.number" class="input-line">
                                    <span class="form-error">{{ errors[0] }}</span>
                                </ValidationProvider>  
                            </div>
                            <div class="col-md-2 mb-25">
                                <label class="label-line">Cep</label>
                                <ValidationProvider rules="required|integer" v-slot="{ errors }">
                                    <input v-model="form.cep" class="input-line" @keyup="searchCep()">
                                    <span class="form-error">{{ errors[0] }}</span>
                                </ValidationProvider>
                            </div>
                            <div class="col-md-2" style="padding-top: 36px">
                                <a class="float-right" href="http://www.buscacep.correios.com.br/sistemas/buscacep/" target="_blank">
                                    <u>Não sei meu CEP</u>
                                </a>
                            </div>
                        </div>

                        <div class="row">
                            <div class="col-md-5 mb-25">
                                <label class="label-line">Rua</label>
                                <ValidationProvider rules="required" v-slot="{ errors }">
                                    <input v-model="form.street" class="input-line">
                                    <span class="form-error">{{ errors[0] }}</span>
                                </ValidationProvider> 
                            </div>
                            <div class="col-md-4 mb-25">
                                <label class="label-line">Bairro</label>
                                <ValidationProvider rules="required" v-slot="{ errors }">
                                    <input v-model="form.district" class="input-line">
                                    <span class="form-error">{{ errors[0] }}</span>
                                </ValidationProvider> 
                            </div>
                            <div class="col-md-2 mb-25">
                                <label class="label-line">Cidade</label>
                                <ValidationProvider rules="required" v-slot="{ errors }">
                                    <input v-model="form.city" class="input-line">
                                    <span class="form-error">{{ errors[0] }}</span>
                                </ValidationProvider>
                            </div>
                            <div class="col-md-1 mb-25">
                                <label class="label-line">UF</label>
                                <ValidationProvider rules="required" v-slot="{ errors }">
                                    <input v-model="form.state" class="input-line">
                                    <span class="form-error">{{ errors[0] }}</span>
                                </ValidationProvider>  
                            </div>
                        </div>

                        <div class="row">
                            <div class="col-md-12 mb-25">
                                <label class="label-line">Complemento</label>
                                <input v-model="form.complement" class="input-line">    
                            </div>
                        </div>

                        <div class="row">
                            <div class="col-md-12 mb-25">
                                <label class="label-line">Descrição</label>
                                <textarea rows="10" style="border: 1px solid #9e9e9e" v-model="form.description" class="form-control"></textarea>
                            </div>
                        </div>

                        <br>
                        <button type="submit" class="btn-general blue float-left">Salvar</button>
                        <br>
                        <br>
                        
                    </form>
                </ValidationObserver>
            </div>
        </div>
    </div>
</template>

<script>
import vue2Dropzone from 'vue2-dropzone'
import 'vue2-dropzone/dist/vue2Dropzone.min.css'

export default {
    name: 'CriarLocal',
    components: {
        'vuedropzone': vue2Dropzone,
        ValidationObserver,
        ValidationProvider
    },
    data: () => ({
        dropzoneOptions: {
            maxFiles: 10,
            url: 'http://localhost:8000/api/upload-file',
            clickable: true,
            params: {
                place_id: null
            },
            addRemoveLinks: true,
            methods: 'post',
            acceptedFiles: '.png, .jpg',
            autoProcessQueue: true,
            dictDefaultMessage: 'Clique aqui ou Arraste para Adicionar as Imagens',
            dictRemoveFile: 'Remover',
            dictMaxFilesExceeded: 'Máximo de 10 imagens'
        },
        alert: {
            status: false,
            title: "",
            type: "",
            message: ""
        },
        form: {
            id: null,
            userId: null,
            imagePath: null,
            intent: 'rent',
            condition: 'residencial',
            type: 'Apartamento',
            phone: null,
            cep: null,
            street: null,
            district: null,
            city: null,
            state: null,
            number: null,
            complement: null,
            area: null,
            rooms: null,
            bathrooms: null,
            suites: null,
            vacancies: null,
            rent_value: null,
            sale_value: null,
            condominium_value: null,
            iptu: null,
            description: null
        },
        apiDomain: 'http://localhost:8000'
    }),
    methods: {
        formSubmit () {
            this.form.userId = window.localStorage.getItem('user')
            let action = this.$route.params.id ? 'place-edit' : 'place-create'
            this.$http.post('http://localhost:8000/api/' + action, this.form).then(response => {
                if (!this.$route.params.id) {
                    this.dropzoneOptions.params.place_id = response.body.id
                    this.$router.push('editar-local/' + response.body.id)
                    this.setAlert('success', 'Sucesso', 'Local Cadastrado com Sucesso')
                    window.scrollTo(0, 0)
                    this.getPlace(response.body.id)
                } else {
                    this.setAlert('success', 'Sucesso', 'Local Editado com Sucesso')
                }
            })
        },
        getPlace (placeId) {
            this.$http.post('http://localhost:8000/api/get-place', {place_id: placeId}).then(response => {                
                this.form = response.body
                this.form.id = response.body.place_id
            })
        },
        setAlert (type, title, message) {
            this.alert.type = type
            this.alert.title = title
            this.alert.message = message
            this.alert.status = true
            setTimeout(() => {
                this.alert.status = false
                this.alert.type = ""
                this.alert.title = ""
                this.alert.message = ""
            }, 5000)
        },
        addedDropZoneProfileFile: function (file, response) {
            file.id = response.id
        },
        removedDropZoneProfileFile: function (file) {
            this.$http.post('http://localhost:8000/api/remove-file', {file_id: file.id})
        },
        addStorageFile: function (files) {
            for (let file of files) {
                if (file) {
                    this.mockFiles = null
                    this.mockFiles = { name: file.name, size: 12345, id: file.id }
                    if (this.$refs.myVueDropzone) {
                        this.$refs.myVueDropzone.manuallyAddFile(this.mockFiles, this.apiDomain + file.path)
                    }
                }
            }
        },
        searchCep () {
            if (this.form.cep && this.form.cep.length === 8) {
                this.$http.get('https://viacep.com.br/ws/' + this.form.cep + '/json/').then(response => {
                    if (response.body.erro === true) {
                    this.form.cep = ''
                    this.form.street = ''
                    this.form.district = ''
                    this.form.city = ''
                    this.form.state = ''
                    } else {
                        let address = response.data
                        this.form.street = address.logradouro
                        this.form.district = address.bairro
                        this.form.city = address.localidade
                        this.form.state = address.uf
                    }
                })
            } else {
                this.form.street = ''
                this.form.district = ''
                this.form.city = ''
                this.form.state = ''
            }
        },
    },
    created () {
        if (this.$route.params.id) {
            this.dropzoneOptions.params.place_id = this.$route.params.id
            this.$http.post('http://localhost:8000/api/get-place-images', {place_id: this.$route.params.id}).then(response => {
                this.addStorageFile(response.body)
            })
            this.getPlace(this.$route.params.id)
        }
        let userId = window.localStorage.getItem('user')
        if (userId) {
            this.$http.post('http://localhost:8000/api/get-user', {user_id: userId}).then(response => {
                this.$store.dispatch('getUser', response.body)
            })
        }
    }
}


import { ValidationObserver, ValidationProvider, extend } from 'vee-validate';
import { required, integer} from 'vee-validate/dist/rules';

extend('required', {
    ...required,
    message: 'Preenchimento obrigatório'
});

extend('integer', {
    ...integer,
    message: 'Deve ser um número'
});


</script>

<style scoped>
</style>
