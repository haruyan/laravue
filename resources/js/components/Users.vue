<template>
  <div class="container">
      <div class="row mt-5">
        <div class="col-md-12">
          <div class="card">
            <div class="card-header">
              <h3 class="card-title">Users Table</h3>

              <div class="card-tools">
                <button class="btn btn-success" @click="newModal"><i class="fas fa-user-plus fa-fw"></i> Add User</button>
              </div>
            </div>
            <!-- /.card-header -->
            <div class="card-body table-responsive p-0">
              <table class="table table-hover">
                <thead>
                  <tr>
                    <th>ID</th>
                    <th>Name</th>
                    <th>Email</th>
                    <th>Type</th>
                    <th>Registered At</th>
                    <th>Modify</th>
                  </tr>
                </thead>
                <tbody>
                  <tr v-for="user in users" :key="user.id">
                    <td>{{ user.id }}</td>
                    <td>{{ user.name }}</td>
                    <td>{{ user.email }}</td>
                    <td>{{ user.type | upText }}</td>
                    <td>{{ user.created_at | myDate }}</td>
                    <td>
                        <a href="#" @click="editModal(user)">
                            <i class="fa fa-edit"></i>
                        </a>
                        /
                        <a href="#" @click="deleteUser(user.id)">
                            <i class="fa fa-trash red"></i>
                        </a>
                    </td>
                  </tr>
                </tbody>
              </table>
            </div>
            <!-- /.card-body -->
          </div>
          <!-- /.card -->
        </div>
      </div>

      <!-- Modal -->
      <div class="modal fade" id="addNew" tabindex="-1" role="dialog" aria-labelledby="addNewLabel" aria-hidden="true">
        <div class="modal-dialog modal-dialog-centered" role="document">
          <div class="modal-content">
            <div class="modal-header">
              <h5 class="modal-title" id="addNewLabel">Add New</h5>
              <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                <span aria-hidden="true">&times;</span>
              </button>
            </div>
            <form @submit.prevent="createUser">
              <div class="modal-body">
                <div class="form-group">
                  <input v-model="form.name" type="text" name="name" placeholder="Name" 
                    class="form-control" :class="{ 'is-invalid': form.errors.has('name') }">
                  <has-error :form="form" field="name"></has-error>
                </div>

                <div class="form-group">
                  <input v-model="form.email" type="email" name="email" placeholder="Email Address" 
                    class="form-control" :class="{ 'is-invalid': form.errors.has('email') }">
                  <has-error :form="form" field="email"></has-error>
                </div>

                <div class="form-group">
                  <textarea v-model="form.bio" type="text" name="bio" placeholder="Some bio (optional)" 
                    class="form-control" :class="{ 'is-invalid': form.errors.has('bio') }"></textarea>
                  <has-error :form="form" field="bio"></has-error>
                </div>

                <div class="form-group">
                  <select v-model="form.type" name="type" id="type" 
                    class="form-control" :class="{ 'is-invalid': form.errors.has('type') }">
                    <option disabled>--Select user role--</option>
                    <option value="admin">Admin</option>
                    <option value="user">Standard User</option>
                    <option value="author">Author</option>
                  </select>
                  <has-error :form="form" field="type"></has-error>
                </div>

                <div class="form-group">
                  <input v-model="form.password" type="password" name="password" placeholder="Password" 
                    class="form-control" :class="{ 'is-invalid': form.errors.has('password') }">
                  <has-error :form="form" field="password"></has-error>
                </div>
              </div>
              <!-- End Body -->
              <div class="modal-footer">
                <button type="button" class="btn btn-danger" data-dismiss="modal">Close</button>
                <button type="submit" class="btn btn-primary">Create</button>
              </div>
            </form>
          </div>
        </div>
      </div>
  </div>
</template>

<script>
import { setInterval } from 'timers';
    export default {
      data() {
        return {
          users: {},
          form: new Form({
            name : '',
            email: '',
            password: '',
            type: '',
            bio: '',
            photo: ''
          })
        }
      },
      methods: {
        editModal(user){
          this.form.reset();
          $('#addNew').modal('show');
          this.form.fill(user);
        },
        newModal(){
          this.form.reset();
          $('#addNew').modal('show');
        },
        deleteUser(id){
          swal.fire({
              title: 'Are you sure?',
              text: "You won't be able to revert this!",
              type: 'warning',
              showCancelButton: true,
              confirmButtonColor: '#3085d6',
              cancelButtonColor: '#d33',
              confirmButtonText: 'Yes, delete it!'
            })
          .then((result) => {
            if(result.value) {
              this.form.delete('api/user/'+id)
                .then(()=>{
                  swal.fire(
                    'Deleted!',
                    'Your file has been deleted.',
                    'success'
                  )
                  Fire.$emit('AfterCreate');
                })
                .catch(()=>{
                  swal.fire('Failed!', 'There was something wrong.', 'warning');
                });
            }
          })
        },

        loadUsers(){
          axios.get('api/user').then( ({data}) => (this.users = data.data) );
        },

        createUser(){
          this.$Progress.start();
          this.form.post('api/user')
          .then(()=>{
            Fire.$emit('AfterCreate');
            $('#addNew').modal('hide');

            toast.fire({
              type: 'success',
              title: 'User Created in successfully'
            });
            this.$Progress.finish();
          })
          .catch(()=>{

          })
        }
      },
      created() {
          this.loadUsers();
          Fire.$on('AfterCreate', () => {
            this.loadUsers();
          });
          // setInterval(() => this.loadUsers(), 3000);
      }
    }
</script>
