<template>
    <div class="send-message">
        <!-- 工具栏 -->
        <div class="header">
            <!-- 表情emoji -->
            <el-popover placement="top" width="400" trigger="click">
                <div class="emoji-wrap">
                    <span v-for="(item, index) in emojiList" :key="index" class="emoji-item"
                        @click="handleEmoji(item.emoji)">{{ item.emoji }}</span>
                </div>
                <div class="icon" slot="reference">
                    <img src="../assets/smile.png" alt="" class="smile" />
                </div>
            </el-popover>
            <!-- 上传图片 -->
            <div class="icon">
                <input id="message-image" type="file" accept="image/png,image/jpeg" class="message-image-input"
                    @change="uploadFile" readonly />
                <label for="message-image" class="sel-image-lab">
                    <i class="el-icon-picture-outline"></i>
                </label>
            </div>
            <!-- 截图 -->
            <div class="icon"><i class="el-icon-scissors" @click="screenShot"></i></div>
        </div>
        <!-- 输入框 -->
        <div contenteditable="true" ref="message" class="textarea" placeholder="请输入消息" @blur="getCursor()"></div>
        <!-- 发送 -->
        <div class="send-btn">
            <el-button type="primary" @click="sendMsg">发送</el-button>
        </div>
        <div id="superMap"></div>
    </div>
</template>

<script>
import ScreenShort from "js-web-screen-shot";
import emojiList from "@/utils/emoji";
import dayjs from "dayjs";
export default {
    name: "SendMessage",
    data() {
        return {
            emojiList,
            message: "",
            cursorPosition: 0
        };
    },
    computed: {
        textarea() {
            return this.$refs.message;
        }
    },
    mounted() {
        this.textarea.focus();
    },
    methods: {
        screenShot() {
            new ScreenShort({
                enableWebRtc: false, // 是否启用webrtc，值为false则使用html2canvas来截图
                loadCrossImg: true, // 跨域
                level: 9999, // 层级
                wrcWindowMode: true,
                completeCallback: ({ base64 }) => {
                    // 将读取的文件添加到编辑器中
                    const img = this.base64ToImage(base64);
                    this.pasteHtmlAtCaret(img);
                }
            });
        },
        getCursor() {
            if (window.getSelection) {
                let selection = window.getSelection();
                this.cursorPosition = selection.getRangeAt(0);
            } else if (document.selection && document.selection.createRange) {
                this.cursorPosition = document.selection.createRange();
            }
        },
        sendMsg() {
            const date = dayjs().format("YYYY-MM-DD HH:mm:ss");
            this.$emit("sendMsg", { msg: this.textarea.innerHTML, date });
            this.textarea.innerHTML = "";
        },
        handleEmoji(val) {
            const node = document.createElement('span');
            node.setAttribute('class','emoji-item')
            node.innerHTML = val;
            this.pasteHtmlAtCaret(node);
        },
        pasteHtmlAtCaret(html) {
            if (window.getSelection) {
                const sel = window.getSelection();
                console.log('html',html)
                this.cursorPosition.insertNode(html);
                if(html){
                    this.cursorPosition = this.cursorPosition.cloneRange();
                    this.cursorPosition.setStartAfter(html);
                    this.cursorPosition.collapse(true);
                    sel.removeAllRanges();
                    sel.addRange(this.cursorPosition);
                }
            } else if (document.selection && document.selection.type != "Control") {
                // IE < 9
                document.selection.createRange().pasteHTML(html);
            }
        },
        fileToBase64(file) {
            return new Promise((resolve) => {
                let reader = new FileReader();
                reader.readAsDataURL(file);
                reader.onload = (e) => {
                    resolve(e.target.result)
                }
            })
        },
        base64ToImage(url) {
            const img = new Image();
            img.src = url
            return img
        },
        base64ToBlob(base64Data, contentType) {
            // base64 解码
            let byteString = window.atob(base64Data.substring(base64Data.indexOf(',') + 1));
            // 创建缓冲数组
            let arrayBuffer = new ArrayBuffer(byteString.length);
            // 创建视图
            let intArray = new Uint8Array(arrayBuffer);
            for (let i = 0; i < byteString.length; i++) {
                intArray[i] = byteString.charCodeAt(i);
            }
            return new Blob([intArray], { type: contentType });
        },
        async readClipboard() {
            try {
                // 粘贴-读取剪切板的内容（异步的）
                const clipboardItems = await navigator.clipboard.read();
                for (const clipboardItem of clipboardItems) {
                    const types = clipboardItem.types;
                    if (types.includes('image/png')) {
                        // 读取纯图片
                        // 得到blob对象（异步的）
                        let blob = await clipboardItem.getType('image/png');
                        // blob转换为file对象
                        const files = new window.File([blob], `${Math.floor(Math.random() * 2147483648).toString(36)}.png`, { type: 'image/png' });
                        const base64Url = await this.fileToBase64(files);
                        const imgElem = this.base64ToImage(base64Url);
                        return imgElem
                    } else if (types.includes('text/html')) {
                        // 图片加文本
                        const blob = await clipboardItem.getType('text/html');
                        const blobText = await blob.text();
                        return blobText
                    } else if (types.includes('text/plain')) {
                        // 纯文本
                        const blob = await clipboardItem.getType('text/plain');
                        const blobText = await blob.text();
                        return blobText
                    }
                }
            } catch (e) {
                console.log('出错了')
            }
        },
        copyToClipboard(blobInput) {
            // 复制-二进制文件存入粘贴板
            const clipboardItemInput = new ClipboardItem({ 'image/png': blobInput });
            if (navigator.clipboard) {
                navigator.clipboard.write([clipboardItemInput]);
            }
        },
        async uploadFile(e) {
            for (const file of e.target.files) {
                // file转base64
                const base64Url = await this.fileToBase64(file);

                // 将读取的文件添加到编辑器中
                const img = this.base64ToImage(base64Url);
                this.pasteHtmlAtCaret(img);

                // 复制-同时将读取的文件放到粘贴板中
                const blob = await this.base64ToBlob(base64Url, 'image/png');
                this.copyToClipboard(blob);
            }
            e.target.value = '';
            return false;
        }
    }
}
</script>

<style scoped lang="scss">
.send-message {
    height: 100%;
    position: relative;
    display: flex;
    flex-direction: column;
}

.send-message .header {
    height: 36px;
    display: flex;
    align-items: center;
}

.send-message .header .icon {
    width: 30px;
    height: 30px;
    display: flex;
    justify-content: center;
    align-items: center;
    margin-right: 5px;
    border-radius: 5px;
    position: relative;

    .smile {
        width: 18px;
        height: 18px;
    }
}

.send-message .header .icon:hover {
    cursor: pointer;
    background-color: #ccc;
}

.message-image-input {
    position: absolute;
    visibility: hidden;
    cursor: pointer;
    width: 30px;
    height: 30px;
    top: 2px;
    left: 0;
}

.el-icon-picture-outline,
.el-icon-scissors {
    font-size: 18px;
    cursor: pointer;
}

.textarea {
    flex: 1;
    padding: 5px;
    box-sizing: border-box;
    overflow: auto;
    outline: none;

    ::v-deep {
        img {
            max-width: 240px;
        }
    }

    &:empty::before {
        content: attr(placeholder);
        font-size: 14px;
        color: #CCC;
        line-height: 21px;
        padding-top: 20px;
    }
}

.emoji-wrap {
    height: 216px;
    background: #fff;
    border-radius: 5px;
    user-select: none;
    overflow: auto;
    box-sizing: border-box;

    .emoji-item {
        font-size: 20px;
        display: inline-block;
        width: 38px;
        text-align: center;
        height: 38px;
        line-height: 38px;
        cursor: pointer;

        &:hover {
            background: #eaeaea;
        }
    }
}

.send-btn {
    text-align: right;
}
</style>
