<template>
    <div class="container">
        <b-form-input
            prepend="@"      
            v-model="searchTerm" 
            placeholder="Search"
            @keyup.enter="onSubmit"
            class="searchbar"
        />
    </div>
</template>

<script>
export default {
    name: 'SearchBar',

    data(){
        return {
            searchTerm: ""
        }
    },

    methods: {
        onSubmit(){
            this.$emit('searchChange', this.searchTerm);
        }
    }
}
</script>

<style scoped>
    @media screen and (min-width: 320px) {
        div{
            padding: 1rem;
        }

        .searchbar{
            position: relative;
            bottom: .2rem;
            background-color: #eff0f5;
        
        } 
    }
    
</style>