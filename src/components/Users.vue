<template>
    <div class="container">
        <div class="row">
            <div class="col-md-12 head-margin-bottom">
                <div class="card">
                    <div class="card-body"> 
                        {{userApiStatus.message}} {{users.length}}
                    </div>
                </div>
            </div>
        </div>
        <div class="row">
            <div class="col-md-12">
                <div class="row">
                    <div class="col-sm-6 cell-margin-bottom" v-for='user in users'>
                        <div class="card">
                            <div class="card-body">
                                <div class="row">
                                    <div class="col-md-3 image-cell">
                                        <img v-bind:src="user.Picture" class="rounded mx-auto d-block" alt="">
                                    </div>
                                    <div class="col-md-9">
                                        <div class="row">
                                            <div class="col-md-12 el-margin-bottom el-cell">{{user.name}}</div>
                                            <div class="col-md-12 el-margin-bottom el-cell">{{user.DOB}}</div>
                                            <div class="col-md-12 el-margin-bottom el-cell">
                                                <a :href="`${user.mailto}`">{{user.Email}}</a>
                                            </div>
                                            <div class="col-md-12 el-margin-bottom el-cell">{{user.Phone}}</div>
                                        </div>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>
                    
                </div>
            </div>
        </div>
    </div>
</template>
<script>
export default {
    name: 'Users',
    data(){
        return {
            userApiStatus: {
                status: false,
                message: 'Default error'
            },
            users: []
        }
    }, 
    methods: {
        async getUsers() {
            let api_status = true;
            let api_message = true;'success'
            const result = await fetch('https://randomuser.me/api/?results=10')
                                .then(this.handleErrors)
                                .then(function(response) {
                                    return response
                                }).catch(function(error) {
                                    api_status = false;
                                    api_message = error;
                                    console.log(error);
                                });
            if(api_status) {
                const {results} = await result.json()
                let result_data = [];
                let that = this
                results.map(
                    function(value, key){
                        let temp_data_arr = [];
                        const   { 
                                    name:{  
                                            title:name_title, 
                                            first:name_first, 
                                            last:name_last
                                    }, 
                                    dob:{
                                        date:dob
                                    },
                                    email:email,
                                    location:{
                                        city:city,
                                        coordinates:{
                                            latitude:latitude,
                                            longitude:longitude
                                        },
                                        country:country,
                                        postcode:postcode,
                                        state:state,
                                        street:{
                                            name:street_name,
                                            number:street_number
                                        }
                                    },
                                    phone:phone,
                                    picture:{
                                        large:picture
                                    }
                                } = value
                        temp_data_arr['sl_no'] = key+1
                        temp_data_arr['name'] = [name_title, name_first, name_last].join(' ')
                        temp_data_arr['DOB'] = that.formatDate(dob)
                        temp_data_arr['Email'] = email
                        temp_data_arr['mailto'] = 'mailto:' + email
                        temp_data_arr['Location'] = {city, latitude, longitude, country, postcode, state, street_name, street_number}
                        temp_data_arr['Phone'] = phone
                        temp_data_arr['Picture'] = picture
                        result_data.push(temp_data_arr);
                        
                    }
                )
                if(result_data.length > 0) {
                    this.userApiStatus.status = true
                    this.userApiStatus.message = 'Data retrieved successfully'
                    this.users = result_data
                }
            } else {
                this.userApiStatus.status = false
                this.userApiStatus.message = api_message
            }
        },
        handleErrors(response) {
            if (!response.ok) {
                throw Error("Invalid API called")
            }
            return response;
        },
        formatDate(dt) {
            const d = new Date(dt);
            return d.toLocaleDateString();
        }
        
    },
    created() {
        this.getUsers()
    }
    
}
</script>
<style scoped>
    .head-margin-bottom,
    .cell-margin-bottom {
        margin-bottom: 15px;
    }
    .el-margin-bottom {
        margin-bottom: 6px;
    } 
    .el-cell{
        text-align: left;
    }
    .image-cell {
        padding: 4px;
    }
</style>
