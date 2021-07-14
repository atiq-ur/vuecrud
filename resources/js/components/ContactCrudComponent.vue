<template>
    <section>
        <div class="mt-2">
            <form @submit.prevent="submit">
                <div class="form-group">
                    <label for="name">Name</label>
                    <input type="text" class="form-control" id="name" v-model="item.name">
                </div>
                <div class="form-group">
                    <label for="email">Email address</label>
                    <input type="email" class="form-control" id="email" aria-describedby="emailHelp"
                           v-model="item.email">
                </div>
                <div class="form-group">
                    <label for="phone_number">Phone Number</label>
                    <input type="text" class="form-control" id="phone_number"
                           v-model ="item.phone_number">
                </div>
                <button type="submit" class="btn btn-primary">
                    {{ isEditing ? 'Update': 'Save'}}</button>
            </form>
        </div>
        <div class="card mt-5" v-if="lists.length > 0">
            <h3 class="text-center p-2">Contact Lists</h3>
            <hr>
            <table class="table table-striped">
                <thead>
                <tr>
                    <th scope="col">#</th>
                    <th scope="col">Name</th>
                    <th scope="col">Email</th>
                    <th scope="col">Phone Number</th>
                    <th scope="col" class="float-right">Actions</th>
                </tr>
                </thead>
                <tbody>
                <tr v-for="(item, index) in lists" :key="item.id">
                    <th scope="row">{{ index+1 }}</th>
                    <td>{{ item.name}}</td>
                    <td>{{ item.email}}</td>
                    <td>{{ item.phone_number}}</td>

                    <td class="float-right">
                        <button class="btn btn-success" @click="editContact(item)">Edit</button>
                    </td>
                    <td>
                        <button class="btn btn-danger"
                                @click="deleteItem(item.id)"
                        >Delete</button>
                    </td>
                </tr>

                </tbody>
            </table>
        </div>

    </section>

</template>

<script>
export default {
    mounted() {
        //console.log('This is Atiqur Rahman')
        this.fetchAll();
    },

    data() {
        return {
            lists: [],
            item: {
                name: "",
                email: "",
                phone_number: "",
            },
            isEditing: false,
            temp_id: null
        }
    },
    methods: {
        fetchAll(){
            try {
                axios.get('/api/contacts')
                    .then(res => this.lists = res.data.data);
            }catch (e) {
                console.log(e)
            }
        },
        submit(){
            let method = axios.post
            let url = '/api/contacts'
            if (this.temp_id){
                method = axios.put;
                url = `/api/contacts/${this.temp_id}`;
            }
            try {
                method(url, this.item)
                    .then(res =>{
                        this.fetchAll();
                        this.item = {
                            name: "",
                            email: "",
                            phone_number: "",
                        }
                        //alert('Uploaded successful');
                        this.temp_id = null;
                        this.isEditing = false;
                    });
            }catch (e) {
                console.log(e);
            }

        },
        editContact(contact){
          this.item ={
              name: contact.name,
              email: contact.email,
              phone_number: contact.phone_number,
          }
          this.temp_id = contact.id;
          this.isEditing = true
        },
        deleteItem(id){
            try{
                axios.delete(`/api/contacts/${id}`)
                    .then( res => this.fetchAll())
            }catch (e) {
                console.log(e)
            }
        }
    }

}
</script>

<style scoped>

</style>
