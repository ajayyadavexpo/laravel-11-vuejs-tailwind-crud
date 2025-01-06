<template>
    <div class="max-w-4xl mx-auto p-4">
        <h1 class="text-3xl font-bold mb-6">Posts</h1>
        
        <form class="mb-6 bg-white p-4 shadow-md rounded-md" @submit.prevent="savePost">
            <div class="mb-4">
                <input type="text" v-model="form.title" placeholder="Title" class="w-full p-2 border border-gray-300 rounded-md focus:outline-none focus:ring focus:ring-indigo-300" />
            </div>

            
            <div class="mb-4">
                <textarea placeholder="Content" v-model="form.content" class="w-full p-2 border border-gray-300 rounded-md focus:outline-none focus:ring focus:ring-indigo-300"></textarea>
            </div>
            
            <button type="submit" class="bg-indigo-500 text-white px-4 py-2 rounded-md hover:bg-indigo-600">{{ editMode ? 'Update' : 'Create' }}</button>

        </form>


        <div v-for="post in posts.data" :key="post.id" class="mb-4 bg-white p-4 shadow-md rounded-md">
            <h3 class="text-xl font-semibold">{{  post.title }}</h3>
            <p class="text-gray-700">{{  post.content }}</p>

            <button type="button" class="bg-yellow-500 text-white px-4 py-2 rounded-md hover:bg-yellow-600 mt-3" @click="editPost(post)">Edit</button>

            <button type="button" class="bg-red-500 text-white px-4 py-2 rounded-md hover:bg-red-600 mt-3 ml-3" @click="deletePost(post.id)">Delete</button>
        </div>


        <!-- Pagination -->
        <div if="posts.links" class="flex justify-center items-center space-x-2 mt-6">
            <button v-for="(link, index) in posts.links" 
                :key="index"
                @click="fetchPosts(link.url)"
                :disabled="!link.url"
                class="px-4 py-2 rounded-md"
                :class="{
                    'bg-indigo-500 text-white hover:bg-indigo-600' :link.active,
                    'bg-gray-500 text-white hover:bg-gray-600' :!link.active && link.url,
                    'bg-gray-300 text-gray-600 cursor-not-allowed' :!link.url
                }"
                v-html="link.label"
                ></button>
        </div>


    </div>
</template>

<script>
import axios from 'axios';


export default{
    data(){
        return {
            posts: {},
            form: {
                title : '',
                content: ''
            },
            editMode:false,
            editId:null
        }
    },
    methods:{
        async fetchPosts(url="/api/posts"){
            const {data} = await axios.get(url);
            this.posts = data;
        },
        async savePost(){
            if(this.editMode){
                await axios.put(`/api/posts/${this.editId}`,this.form);
                this.editMode = false;
            }else{
                await axios.post('/api/posts',this.form);
            }
            this.form = {
                title : '',
                content : '',
            }
            this.fetchPosts();
        },
        editPost(post){
            this.form={
                title: post.title,
                content: post.content,
            };
            this.editId = post.id;
            this.editMode = true;
        },
        async deletePost(id){
            await axios.delete(`/api/posts/${id}`);
            this.fetchPosts();
        }
    },
    mounted(){
        this.fetchPosts();
    }
}

</script>