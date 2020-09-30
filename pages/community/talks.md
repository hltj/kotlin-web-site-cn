---
title: Kotlin 活动
layout: default-v2
---

# Kotlin 会谈与演讲者支持

如果是 Kotlin 演讲者，请填写[此表格](https://surveys.jetbrains.com/s3/Submit-a-Kotlin-Talk)告知 JetBrains 即将进行的演讲，JetBrains 将在 <kotlinlang.org> 上公布演讲。
JetBrains 也会很乐意寄一些T恤以及贴纸与赠品给活动的参加者。

<br/>


<div id="events" class="events">

    <div class="events-list-col">
        <div class="events-list js-events-list"></div>
    </div>

    <div class="events-map-col">
        <div class="js-events-map-panel-wrap">
            <div class="events-map-panel js-events-map-panel">
                <div class="map js-map"></div>
            </div>
        </div>
    </div>

</div>

<script src="{{ url_for('static', filename='events.js') }}"></script>
<link rel="stylesheet" href="{{ url_for('static', filename='events.css')|autoversion }}">