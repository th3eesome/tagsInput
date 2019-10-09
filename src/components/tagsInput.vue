<!-- 组件说明 -->
<template>
    <div class='field' @click="fieldClick" ref="field">
        
            <template v-for="(item, index) in tags">
                    <span v-if="modifyIndex !== index" :class="current === index ? 'tag-focus' :'tag'" @click="selectTag(index)" :key="`tag_${index}`" @dblclick="dblclick">
                            {{item}}
                            <span @click="deleteItem($event, index)" class="close_tag">
                                <span id="close"></span>
                            </span>
                    </span>
                    <input class="modify_input" ref="modify_input" v-model="modifyItem" v-else :key="`input_${index}`" @blur="inputBlur('modify')"  @keydown="keyDown($event,'modify')"/>
            </template>
        
        <input class="add_input" ref="add_input" v-model="item" @blur="inputBlur('add')" @keydown="keyDown($event, 'add')" @focus="addFocus"/>
    </div>
</template>

<script>
    //import x from ''
    export default {
        name: 'tags-input',
        components: {

        },
        props: {
            data: {
                type: Array,
                default: () => []
            },
            separators: {
                type: String,
                default: () => {
                    return '';
                }
            }
        },
        data () {
            return {
                item: '',
                tags: this.data,
                current: -1,
                modifyIndex: -1,
                modifyItem: ''
            };
        },
        created() {
            window.addEventListener('keydown', this.docKeysDown);
        },
        methods: {
            docKeysDown($event) {
                if (this.current < 0) {
                    return;
                }
                let last = this.current === this.tags.length - 1;
                if ($event.which === 8) {
                    let oldIndex = this.current;
                    this.deleteItem($event, this.current);
                    this.current = last ? this.tags.length - 1 : oldIndex - 1;
                } else if ($event.which === 37) {
                    this.current === 0 ? this.current = 0 : this.current--;
                } else if ($event.which === 39) {
                    if (last) {
                        this.current = -1;
                        this.$refs.add_input.focus();
                    } else {
                        this.current++;
                    }
                } else if ($event.which === 13) {
                        let oldvalue = this.tags[this.current];
                        this.modifyIndex = this.current;
                        this.modifyItem = oldvalue;
                        this.current = -1;
                        this.$nextTick(() => {
                            this.$refs.modify_input[0].focus();
                        })
                }
            },
            inputBlur(type) {
                type === 'add' ? this.generate() : this.modified();
            },
            generate() {
                if (!this.item) {
                    return;
                }
                this.tags.push(this.item);
                this.item = '';
            },
            modified() {
                this.modifyItem ? this.tags.splice(this.modifyIndex, 1, this.modifyItem) : this.tags.splice(this.modifyIndex, 1);
                this.modifyItem = '';
                this.modifyIndex = -1;
            },
            keyDown($event, type) {
                if (this.separators.indexOf($event.code) > -1) {
                    $event.preventDefault();
                    if (type === 'add')
                        this.generate();
                    else {
                        this.$refs.modify_input[0].blur();
                        this.$refs.add_input.focus();
                    }
                }   if ($event.which === 8) {
                    $event.stopPropagation();
                    if (this.item === '') {
                        this.$refs.add_input.blur();
                        this.current = this.tags.length - 1;
                    }
                } else if ($event.which === 37) {
                    $event.stopPropagation();
                    if (this.item === '') {
                        this.$refs.add_input.blur();
                        this.current = this.tags.length - 1;
                    }
                }
            },
            fieldClick(e) {
                if (e.target.className === 'field') {
                    this.$refs.add_input.focus();
                } 
            },
            deleteItem($event, index) {
                $event.stopPropagation(); // 我们不希望在点击删除后，事件冒泡到tag上，从而触发tag的focus行为。
                this.tags.splice(index, 1);
                this.current = -1;
            },
            selectTag(index) {
                this.current = index;
            },
            addFocus() {
                this.current = -1;
            },
            dblclick() {
                let oldvalue = this.tags[this.current];
                        this.modifyIndex = this.current;
                        this.modifyItem = oldvalue;
                        this.current = -1;
                        this.$nextTick(() => {
                            this.$refs.modify_input[0].focus();
                        })
            }
        },
    }
</script>

<style lang='scss' scoped>
//@import url()
.field {
    text-align: start;
    width: 300px;
    height: 160px;
    border: 1px solid gray;
}
.tag {
    display: inline-block;
    background-color: #77bfe0;
    margin: 3px;
    padding: 2px;
    border-radius: 4px;
    color: whitesmoke
}

.tag-focus {
    @extend .tag;
    background-color: #64a5c4
}
.list-enter-active {
  transition: all 100ms;
}
.list-enter
/* .list-leave-active for below version 2.1.8 */ {
  opacity: 0;
}
.add_input, .modify_input {
    outline-style: none;
    border: none;
    margin: 3px;
    padding: 3px;
    width: 80px;

}
.add_input:focus {
    border: 1px dashed rgba(180, 180, 180, 0.589);
    border-radius: 4px;
}

.modify_input:focus {
    @extend .add_input, :focus;
    margin: 3px 3px 0 3px;
}


.close_tag {
    cursor: pointer;
}
#close {
    display: inline-block;
    width: 12px;
    height: 2px;
    background: whitesmoke;
    transform: rotate(45deg);
    vertical-align: middle;
    margin-bottom: 2px;
}
#close::after {
    content: '';
    display: block;
    width: 12px;
    height: 2px;
    background: whitesmoke;
    transform: rotate(-90deg);
}
</style>