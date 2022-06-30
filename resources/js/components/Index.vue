<template>
    <div class="w-25">
        <input v-model='title' type="text" class="mb-3 form-control" placeholder="title">
        <div ref="dropzone" class="mb-3 btn d-block p-5 bg-dark text-center text-light">Upload</div>
        <input @click.prevent="store" type="submit" class="btn btn-primary" value="add">

        <div class="mt-5" v-if="post">
            <h4>{{ post.title }}</h4>
            <div v-for="image in post.images" class="mb-3">
                <img :src="image.url" width="300">
            </div>
        </div>
    </div>
</template>

<script>
    import Dropzone from 'dropzone'

    export default {
        name: "Index",

        data() {
            return {
                dropzone: null,
                title: '',
                post: null
            }
        },

        mounted() {
            this.dropzone = new Dropzone(this.$refs.dropzone, {
                url: "/api/posts",
                autoProcessQueue: false,
                addRemoveLinks: true
            })
            this.getPost()
        },

        methods: {
            store() {
                const data = new FormData()
                const files = this.dropzone.getAcceptedFiles()
                files.forEach(file => {
                    data.append('images[]', file)
                    this.dropzone.removeFile(file)
                })
                this.title = ''
                data.append('title', this.title)

                axios.post('/api/posts', data)
                    .then(res => {
                        this.getPost()
                    })

            },

            getPost() {
                axios.get('/api/posts')
                    .then(res => {
                        this.post = res.data.data
                    })
            }
        }
    }
</script>

<style scoped>

</style>
