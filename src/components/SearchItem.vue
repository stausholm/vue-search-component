<template>
    <li @click="emitData">
        <div class="country-header">
            <span>{{country.alpha2Code}}</span>
            <div>
                <p>{{country.name}}</p>
                <p>({{country.nativeName}})</p>
            </div>
        </div>
        <div class="country-info">
            Population: {{country.population}}
            Capital: {{country.capital}}
            Area: {{country.area}} km^2
        </div>
    </li>
</template>

<script>
import {bus} from '../main';

export default {
    props: ['country'],
    methods: {
        emitData: function() {
            bus.$emit('itemClicked', this.country);
        }
    }
}
</script>

<style lang="scss" scoped>
li {
    margin-bottom: 10px;
    transition: opacity .4s;
    .country-header {
        background: #ddd;
        padding: 10px;
        display: flex;
        border-top-left-radius: 10px;
        border-top-right-radius: 10px;
        span {
            width: 40px;
            height: 40px;
            display: block;
            line-height: 40px;
            text-align: center;
            border-radius: 50%;
            background: grey;
            color:#fff;
            margin-right: 10px;
        }
        div p:nth-child(2) {
            font-size: .65em;
            opacity: .7;
        }
    }
    .country-info {
        padding: 10px;
        border: 1px solid #ddd;
        border-bottom-left-radius: 5px;
        border-bottom-right-radius: 5px;
    }
}
</style>
