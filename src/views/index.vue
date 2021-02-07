<template>
    <div>
        <!-- chat Room -->
        <div class="chat-room">
            <!-- chat content -->
            <div class="chat-content">
                <div :class="['message', {'my-message': item.type === 0}, {'other-message': item.type === 1}]" v-for="(item, index) in chatList" :key="index">
                    <span class="message-item">{{ item.message }}</span>
                </div>
            </div>
            <!-- input -->
            <div class="input">
                <textarea type="text" maxlength="100" class="textarea" v-model="message"></textarea>
                <button class="button submit-btn" @click="submit">发送</button>
                <button class="button cancel-btn" @click="cancel">清除</button>
            </div>
        </div>
    </div>
</template>
<script>
export default {
    data() {
        return {
            socket: null,
            message: '',
            chatList: []
        }
    },
    created() {
        this.initCoon()
    },
    methods: {
        initCoon() {
            if (typeof WebSocket === 'undefined') {
                alert('抱歉，您的浏览器不支持Websocket')
            } else {
                const path = 'ws://localhost:3000/'
                this.socket = new WebSocket(path)
                this.socket.open = this.open
                this.socket.onerror = this.error
                this.socket.onclose = this.closed
                this.socket.onmessage = this.getMessage
                 window.onbeforeunload = function(e) {
                    e = e || window.event
                    if (e) {
                        e.returnValue = "关闭提示"
                        socket.close()
                    }
                    socket.close();
                    return "关闭提示"
                }
            }
        },
        open() {
            alert('连接成功了！')
        },
        error() {
            alert('连接失败了！')
        },
        closed() {
            alert('关闭连接了！')
        },
        getMessage(msg) {
            this.chatList.push({
                message: JSON.parse(msg.data).txt
            })
        },
        submit() {
            if (this.message !== '') {
                const obj = {
                    txt: this.message
                }
                this.socket.send(JSON.stringify(obj))
            }
        },
        cancel() {
            this.message = ''
        }
    }
}
</script>
<style scope>
    .chat-room {
        position: absolute;
        top: 50%;
        left: 50%;
        transform: translate(-50%, -50%);
        border: 1px solid #aaa;
    }
    .chatting-num {
        padding: 20px 0;
        margin: 20px;
        background: #409eff;
        text-align: center;
        color: #fff;
        border-radius: 10px;
    }
    .chat-content {
        padding: 20px 0;
        margin: 20px;
        height: 300px;
        overflow-y: auto;
	    -webkit-overflow-scrolling: touch;
    }
    .message {
        display: flex;
        align-items: center;
        margin-bottom: 10px;
    }
    .my-message {
        justify-content: flex-end;
    }
    .other-message {
        justify-content: flex-start;
    }
    .message-item {
        max-width: 40%;
        background-color: #409eff;
        color: #fff;
        border-radius: 10px;
        padding: 10px;
    }
    .input {
        display: flex;
        justify-content: space-around;
        align-items: center;
        border-top: 1px solid #aaa;
        padding: 20px;
    }
    .textarea {
        resize: none;
        width: 50%;
        height: 50px;
    }
    .button {
        border-radius: 10px;
        padding: 10px 20px;
        outline: none;
    }
    .button:hover {
        cursor: pointer;
    }
    .button:active {
        box-shadow: 2px 2px 10px #aaa;
    }
    .submit-btn {
        border: none;
        background-color: darkcyan;
        color: #fff;
    }
    .cancel-btn {
        border: 1px solid #aaa;
        background-color: #fff;
        color: #000;
    }
</style>