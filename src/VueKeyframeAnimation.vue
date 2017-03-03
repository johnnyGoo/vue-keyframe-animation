<template>
    <div>
        <slot></slot>
    </div>
</template>


/*!
* vue-keyframe-animation v0.0.1 (https://github.com/johnnyGoo/vue-keyframe-animation)
* Author: Johnny chen
*
* Copyright 2013-2016 Johnny chen
*/
<script>


    var count = 0;

    if (!window.Smart) {
        throw 'vue-keyframe-animation required smart.js'
    }
    var Smart = window.Smart;
    var Css = Smart.Css;
    var _ = Smart._;
    function getCurrentObj(from, to, val) {
        var obj = {value: val, css: {}};
        var percent = (val - from.value) / (to.value - from.value);
        _.each(to.css, function (v, k) {
            if (from.css.hasOwnProperty(k)) {
                obj.css[k] = from.css[k] + (to.css[k] - from.css[k]) * percent
            }
        })
        return obj;

    }


    // 注册
    export default {
        // 声明 props
        props: {
            keyframes: {
                type: Array,
                default: function () {
                    return [{value: 0, css: {}}, {value: 1, css: {}}]
                }
            },
            current: {
                type: Number,
                default: 0
            },
            time: {
                type: String,
                default: '0.1s'
            },
            active:{
                type: Boolean,
                default:true
            }
        },
        data: function () {
            return {
                keyframes: [],
                current: 0
            }
        },
        methods: {
            updateFrame: function (val) {
                var to;
                _.each(this.keyframes, function (v) {
                    if (v.value == val) {
                        to = v;
                    }
                });
                if(val<=this.keyframes[0].value){
                    to = this.keyframes[0];
                }else if(val>=this.keyframes[this.keyframes.length-1].value){
                    to = this.keyframes[this.keyframes.length-1];
                }
                if (!to) {
                    for (var i = 0; i < this.keyframes.length; i++) {
                        var key = this.keyframes[i];
                        if (Number(key.value) > val) {
                            var v1, v2;
                            v1 = this.keyframes[i - 1];
                            v2 = key;
                            to = getCurrentObj(v1, v2, val);
                            break;
                        }
                    }
                }


                Css.smartCss(this.$el, to.css);
            }
        },

        watch: {
            current: function (val, oldVal) {
                if(!this.active){
                    return;
                }
//                if (val < 0) {
//                    this.current = 0;
//                    return;
//                }
//                if (val > 1) {
//                    this.current = 1;
//                    return;
//                }
                this.updateFrame(val);

            }
        },
        computed: {},


        ready: function () {
            _.each(this.keyframes, function (v) {
                v.value = Number(v.value);
            });
            this.updateFrame(this.current);
            Css.smartCss(this.$el, {transition: 'all ' + this.time});

        }


    }


</script>