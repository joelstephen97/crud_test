<template>
    <button @click="togglePosts">Show Posts</button>
    <div>
        <input v-model="fetchSinglePostId" placeholder="Post Id" />
        <button @click="fetchSinglePost(fetchSinglePostId)">Fetch Single Post Id</button>
        <div v-if="singlePost" :key="singlePost.id">
            <h2>{{ singlePost.userId }}</h2>
            <h2>{{ singlePost.title }}</h2>
            <p>{{ singlePost.body }}</p>
            <button @click="existingPost = singlePost">Edit</button>
            <div v-if="existingPost.id === singlePost.id">
                <input v-model="existingPost.userId" type="number" />
                <input v-model="existingPost.title" placeholder="Title" />
                <input v-model="existingPost.body" placeholder="Body" />
                <button @click="editPost(singlePost.id, existingPost)">Edit Post</button>
            </div>
        </div>

    </div>
    <div v-if="showPosts" v-for="post in posts">
        <div :key="posts.id">
            <span>
                <h2>{{ post.userId}}</h2>
                <h2>{{ post.title }}</h2>
            </span>
            <p>{{ post.body }}</p>
            <button @click="existingPost = post">Edit</button>
            <div v-if="existingPost.id === post.id">
                <input v-model="existingPost.userId" type="number" />
                <input v-model="existingPost.title" placeholder="Title" />
                <input v-model="existingPost.body" placeholder="Body" />
                <button @click="editPost(post.id, existingPost)">Edit Post</button>
            </div>
        </div>
    </div>
    <div>
        <h2>New Post</h2>
        <input v-model="newPost.userId" type="number" />
        <input v-model="newPost.title" placeholder="Title" />
        <input v-model="newPost.body" placeholder="Body" />
        <button @click="addPost(newPost)">Add Post</button>
    </div>
</template>

<script setup lang="ts">
import CallAPI from '../components/CallApi'
import axios from 'axios'
import { mkConfig, generateCsv, download } from "export-to-csv";
const csvConfig = mkConfig({ useKeysAsHeaders: true });
interface Post {
    userId: number
    id: number
    title: string
    body: string
}


const posts = ref([]) 
const singlePost = ref({})
const fetchSinglePostId = ref<String>()

const getLastPostId = computed(() => {
    posts.value.sort()
    return posts.value[post.value.length - 1].id
})
const newPost = ref({
    userId: 0,
    title: '',
    body: '',
})
const existingPost = ref({
    id: 0,
    userId: 0,
    title: '',
    body: '',
})

const showPosts = ref(false)

const togglePosts = () => {
    getPosts()
    showPosts.value = !showPosts.value
}

const getPosts = async() => {
    axios.get('https://jsonplaceholder.typicode.com/posts')
        .then((response) => response.data)
        .then((data) => posts.value = data)
}

const addPost = async (value:Post) => {
    fetch('https://jsonplaceholder.typicode.com/posts', {
        method: 'POST',
        body: JSON.stringify({
            ...value
        }),
        headers: {
            'Content-type': 'application/json; charset=UTF-8',
        },
    }).then(response => {
        newPost.value = {
            userId: 0,
            title: '',
            body: '',
        }
    }).then(getPosts())
}

const editPost = async (id,value) => {
    fetch('https://jsonplaceholder.typicode.com/posts/' + id, {
        method: 'PUT',
        body: JSON.stringify({
            ...value
        }),
        headers: {
            'Content-type': 'application/json; charset=UTF-8',
        },
    }).then(response => {
        existingPost.value = {
            id: 0,
            userId: 0,
            title: '',
            body: '',
        }
    }).then(getPosts())
}

const fetchSinglePost = (id:String) => {
    fetch('https://jsonplaceholder.typicode.com/posts/'+id)
        .then((response) => response.json())
        .then((json) => singlePost.value = json)
}


</script>