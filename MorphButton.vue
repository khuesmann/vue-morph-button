<template>
    <div class="morph-button">
        <div class="morph-button-triggerButton" @click="isActive = true"><slot name="button"></slot></div>
        <div class="morph-button-content" v-show="isActive" :style="contentStyle">
            <div class="morph-button-content-wrapper">
                <slot name="content"></slot>
            </div>
        </div>
        <div class="morph-button-overlay" @click="hide()" :class="{'show-content': isActive}">
            <a @click="hide()" class="morph-button-close">
                <svg-icon color="#fff" :icon="icons.close" :size="20"></svg-icon>
            </a>
        </div>
    </div>
</template>

<script>
    import SvgIcon from './SvgIcon.vue'
    import iconClose from '../static/images/icons/close.svg'


    export default {
        components: { SvgIcon },
        data() {
            return {
                isActive: false,
                showContent: false,
            }
        },
        props: {
            contentWidth: {
                type: String,
                default: "80%"
            },
            contentHeight: {
                type: String,
                default: "70%"
            },
            close: {
                type: String,
                default: "close"
            },
            contentStyle: {
                type: String,
                default: ""
            },
        },
        computed:{
            triggerButton() {
                return this.$el.children[0];
            },
            content() {
                return this.$el.children[1];
            },
            overlay() {
                return this.$el.children[2];
            },
            icons() {
                return {
                    close: iconClose
                }
            }
        },
        mounted() {
        },
        watch: {
            isActive(isActive) {
                setTimeout(() => {
                    if(isActive) return this.show();
                })
            }
        },
        methods: {
            setContentInitDimensions(resetTransition = true) {
                let buttonBounding = this.triggerButton.getBoundingClientRect();
                if(resetTransition) this.content.style.transition = 'opacity 0.4s 0.1s';
                this.content.style.height = this.triggerButton.offsetHeight + 'px';
                this.content.style.width = this.triggerButton.offsetWidth + 'px';
                this.content.style.top = buttonBounding.top + 'px';
                this.content.style.left = buttonBounding.left + 'px';
                this.content.style.visibility = 'visible';
                this.content.style.opacity = 1;
            },
            centerContent() {
                this.content.style.height = this.contentHeight
                this.content.style.width = this.contentWidth
                this.content.style.top = `calc((100vh - ${this.contentHeight}) / 2)`;
                this.content.style.left = `calc((100vw - ${this.contentWidth}) / 2)`;
            },
            toggleContentTransition(show) {
                if(show) this.content.style.transition = 'width 0.4s 0.1s, height 0.4s 0.1s, top 0.4s 0.1s, left 0.4s 0.1s, opacity 0.4s 0.1s';
                else this.content.style.transition = ''
            },
            show() {
                this.showContent = true;
                this.setContentInitDimensions();
                this.overlay.style.opacity = 1;
                setTimeout(() => {
                    this.triggerButton.style.opacity = 0;
                    this.toggleContentTransition(true);
                    this.centerContent()
                    setTimeout(() => {
                        this.content.className += ' show-content'
                    }, 200)
                }, 100)

            },
            hide() {
                this.content.className = this.content.className.replace(' show-content', '');
                this.setContentInitDimensions(false);
                this.overlay.style.opacity = 0;
                setTimeout(() => {
                    this.triggerButton.style.opacity = 1;
                    this.content.style.opacity = 0;
                    this.isActive = false;
                }, 500)
            }
        }
    }
</script>

<style lang="scss">
    @import "../assets/scss/variables.scss";

    .morph-button {
        position: relative;
        /*display: inline-block;*/
    }
    .morph-button-trigger {
        opacity: 1;
        transition: opacity 0.8s 0.1s
    }
    .morph-button-content {
        background-color: white;
        border: 1px solid #cccccc;
        border-radius: 5px;
        height: 0;
        width: 0;
        position: fixed;
        top: 0;
        left: 0;
        right: 0;
        bottom: 0;
        overflow: hidden;
        visibility: hidden;
        opacity: 0;
        transition: opacity 0.3s;
        z-index: $z-index-modal;
        box-shadow: 0px 0px 20px rgba(0,0,0,0.4);

        * {
            transition: opacity 0.3s;
            opacity: 0;
        }

        &.show-content {
            overflow: auto;
            * {
                opacity: 1;
            }
        }

        .morph-button-content-wrapper {
            position: relative;
            padding-top: 15px;
        }
    }

    .morph-button-overlay {
        width: 100vw;
        height: 100vh;
        background-color: rgba(0,0,0,0.4);
        z-index: $z-index-modal - 1;
        opacity: 0;
        visibility: hidden;
        transition: opacity 0.4s 0.1s;
        position: fixed;
        top:0;
        left: 0;

        &.show-content {
            visibility: visible;
            opacity: 1;
        }
    }

    .morph-button-close {
        position: fixed;
        top: 17px;
        right: 10px;
        color: white;
        cursor: pointer;
        line-height: 0;
        padding: 6px;
    }
</style>
