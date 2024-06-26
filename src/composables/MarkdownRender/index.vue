<script setup lang="ts">
import { computed, defineProps } from 'vue'

const props = defineProps({
  content: {
    type: String,
    default: '',
  },
})
const content = computed(() => props.content)
</script>

<template>
  <div class="markdownContainer">
    <content class="markdown-body" />
  </div>
</template>

<style scoped lang="scss">
// https://imageslr.github.io/mweb-themes/
.markdownContainer {
    margin-bottom: 150px;

    :deep(.markdown-body) {
        max-width: 66.4vw;
        margin: 0 auto;
        padding: 1em;
        word-break: break-word;
        line-height: 1.8;
        font-weight: 400;
        font-size: 16px;
        word-spacing: 2px;
        letter-spacing: 2px;
        overflow-x: hidden;
        color: #3e3e3e;

        /* 段落 */
        p {
            color: #595959;
            font-size: 15px;
            line-height: 2;
            font-weight: 400;
        }

        /* 段落间距控制 */
        p + p {
            margin-top: 16px;
        }

        /* 标题的通用设置 */
        h1,
        h2,
        h3,
        h4,
        h5,
        h6 {
            padding: 30px 0;
            margin: 0;
            color: #135ce0;
        }

        /* 一级标题 */
        h1 {
            position: relative;
            text-align: center;
            font-size: 28px;
        }

        /* 二级标题 */
        h2 {
            position: relative;
            font-size: 24px;
            border-left: 4px solid;
            padding: 0 0 0 10px;
        }

        /* 三级标题 */
        h3 {
            font-size: 16px;
        }

        /* 无序列表 */
        ul {
            list-style: disc outside;
            margin-left: 2em;
            margin-top: 1em;
        }

        /* 无序列表内容 */
        li {
            line-height: 2;
            color: #595959;
            margin-bottom: 0;
            list-style: inherit;
        }

        img {
            max-width: 100%;
        }

        /* 已加载图片 */
        img.loaded {
            margin: 0 auto;
            display: block;
        }

        /* 引用 */
        blockquote {
            background: #fff9f9;
            margin: 2em 0;
            padding: 2px 20px;
            border-left: 4px solid #b2aec5;
        }

        /* 引用文字 */
        blockquote p {
            color: #666;
            line-height: 2;
        }

        /* 链接 */
        a {
            color: #036aca;
            border-bottom: 1px solid rgba(3, 106, 202, 0.8);
            font-weight: 400;
            text-decoration: none;
        }

        /* 加粗 */
        strong {
            color: #036aca;
        }

        /* 加粗斜体 */
        em strong {
            color: #036aca;
        }

        /* 分隔线 */
        hr {
            border-top: 1px solid #135ce0;
        }

        /* 代码 */
        pre {
            overflow: auto;
        }

        pre,
        code {
            overflow: auto;
            position: relative;
            line-height: 1.75;
            font-family:
                Menlo,
                Monaco,
                Consolas,
                Courier New,
                monospace;
        }

        pre > code {
            /* font-size: 12px; */
            padding: 15px 12px;
            margin: 0;
            word-break: normal;
            display: block;
            overflow-x: auto;
            color: #333;
            background: #f8f8f8;
        }

        code {
            word-break: break-word;
            border-radius: 2px;
            overflow-x: auto;
            background-color: #fff5f5;
            color: #ff502c;
            font-size: 0.87em;
            padding: 0.065em 0.4em;
        }

        /* 表格 */
        table {
            border-collapse: collapse;
            margin: 1rem 0;
            overflow-x: auto;
            width: 100%;
        }

        table th {
            background-color: #f6f8fa;
        }

        table th,
        table td {
            border: 1px solid #dfe2e5;
            padding: 0.6em 1em;
        }

        table tr {
            border-top: 1px solid #dfe2e5;
        }

        table tr:nth-child(2n) {
            background-color: #fafafa;
        }

        /* 删除了调整 font-size、padding 的选项 */
        /* PrismJS 1.23.0
https://prismjs.com/download.html#themes=prism&languages=markup+css+clike+javascript */
        /**
* prism.js default theme for JavaScript, CSS and HTML
* Based on dabblet (http://dabblet.com)
* @author Lea Verou
*/
        code[class*='language-'],
        pre[class*='language-'] {
            color: black;
            text-shadow: 0 1px white;
            font-family: Consolas, Monaco, 'Andale Mono', 'Ubuntu Mono', monospace;
            /* font-size: 1em; */
            text-align: left;
            white-space: pre;
            word-spacing: normal;
            word-break: normal;
            word-wrap: normal;
            line-height: 1.5;
            -moz-tab-size: 4;
            -o-tab-size: 4;
            tab-size: 4;
            -webkit-hyphens: none;
            -moz-hyphens: none;
            -ms-hyphens: none;
            hyphens: none;
        }

        pre[class*='language-']::-moz-selection,
        pre[class*='language-'] ::-moz-selection,
        code[class*='language-']::-moz-selection,
        code[class*='language-'] ::-moz-selection {
            text-shadow: none;
            background: #b3d4fc;
        }

        pre[class*='language-']::selection,
        pre[class*='language-'] ::selection,
        code[class*='language-']::selection,
        code[class*='language-'] ::selection {
            text-shadow: none;
            background: #b3d4fc;
        }

        @media print {
            code[class*='language-'],
            pre[class*='language-'] {
                text-shadow: none;
            }
        }

        /* Code blocks */
        pre[class*='language-'] {
            /* padding: 1em; */
            margin: 0.5em 0;
            overflow: auto;
        }

        :not(pre) > code[class*='language-'],
        pre[class*='language-'] {
            background: #f5f2f0;
        }

        /* Inline code */
        :not(pre) > code[class*='language-'] {
            padding: 0.1em;
            border-radius: 0.3em;
            white-space: normal;
        }

        .token.comment,
        .token.prolog,
        .token.doctype,
        .token.cdata {
            color: slategray;
        }

        .token.punctuation {
            color: #999;
        }

        .token.namespace {
            opacity: 0.7;
        }

        .token.property,
        .token.tag,
        .token.boolean,
        .token.number,
        .token.constant,
        .token.symbol,
        .token.deleted {
            color: #905;
        }

        .token.selector,
        .token.attr-name,
        .token.string,
        .token.char,
        .token.builtin,
        .token.inserted {
            color: #690;
        }

        .token.operator,
        .token.entity,
        .token.url,
        .language-css .token.string,
        .style .token.string {
            color: #9a6e3a;
            /* This background color was intended by the author of this theme. */
            background: rgba(255, 255, 255, 0.5);
        }

        .token.atrule,
        .token.attr-value,
        .token.keyword {
            color: #07a;
        }

        .token.function,
        .token.class-name {
            color: #dd4a68;
        }

        .token.regex,
        .token.important,
        .token.variable {
            color: #e90;
        }

        .token.important,
        .token.bold {
            font-weight: bold;
        }

        .token.italic {
            font-style: italic;
        }

        .token.entity {
            cursor: help;
        }
    }
}
</style>
