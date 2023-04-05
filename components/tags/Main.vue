<template>
    <div>
        <div class="ml-[800px]">
            <!--Start of add tag-->
            <TagsAdd @add="add" />
            <!--End of add tag-->
        </div>
        <div v-if="allTags">
            <!--Start of tags-->
           <TagsList :allTags="allTags" :alphabets="alphabets" @edit="edit" @delete="deleteTag" />
            <!--End of tags-->
        </div>
    </div>
</template>

<script setup lang="ts">
import { defineProps, ref } from 'vue'
import { PencilIcon, XMarkIcon } from '@heroicons/vue/24/outline'
// Tgs json props
interface TagProps {
    url: string
    entity: string // Entity for the api call
    entityId: string // Entity id for the api call
    projectId: string // Project id
}
// Tags props default data
const props = withDefaults(defineProps<TagProps>(), {
    url: '',
    entity: '',
    entityId: '',
    projectId: '',
})
// To edit selected tag by sending the uid
const editSelectedTag = ref([])
// Get tags from app config
const authHeader = { Authorization: 'Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1IjoiNjgzZGM0MDhmMjFmNDIyYjk1NmY5YjQxMzZmYjRjYTMiLCJkIjoiMTY4MDA0NiIsInIiOiJzYSIsInAiOiJmcmVlIiwiYSI6ImZpbmRlci5pbyIsImwiOiJ1czEiLCJleHAiOjE2ODMxNzY2NTh9.mQOm1NxFMiIcVmhpz_FKpzN-Cn3rRZWxDG3u9NbQg-M' };
const { pending, data: tags } = useLazyFetch(`${props.url}/entity/${props.entity}?offset=0&limit=100&sort_column=id&sort_direction=desc`, {method:"GET",headers:authHeader})
const allTags = tags.value
// Get alphabets to show the tags in the order
const alphabets = ref([...Array(26)].map((_, i) => String.fromCharCode(65 + i)))
// Add - add to database and create tag
const add = async (name: string) => {
    await useLazyFetch(`${props.url}/`, {method:"POST",headers:authHeader,
        body: {
            project_id: props.projectId,
            name,
            entity: props.entity,
        },
    }
    )
    // Push new tag into existing tags
    allTags.push(name)
}
const editTag = (tag: any) => {
    // Push the tag UID into the editSelectedTag array
    editSelectedTag.value.push(tag.name)
}
// Edit -  edit tags in database
const edit = async (tag: any) => {
    await useLazyFetch(`${props.url}/${tag.uid}?name=${tag.name}`, {method:"PUT",headers:authHeader})
    allTags.forEach((data: any) => {
        if (tag.uid === data.uid) {
            data.name = tag.name
        }
    })
}
// Delete - delete tag in database
const deleteTag = async (tag: any, index: number) => {
    console.log("hello",tag,index)
    await useLazyFetch(`${props.url}/${tag.uid}`, {method:"DELETE",headers:authHeader})
    // If the tag exists, delete it
    if (index !== -1) {
        // To remove deleted tag
        allTags.splice(index, 1)
    }
}
</script>
