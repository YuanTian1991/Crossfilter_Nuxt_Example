<template>
  <svg
    xmlns="http://www.w3.org/2000/svg"
    xmlns:xlink="http://www.w3.org/1999/xlink"
    width="113px"
    height="113px"
    content="&lt;mxfile modified=&quot;2018-12-27T01:08:18.037Z&quot; host=&quot;www.draw.io&quot; agent=&quot;Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/70.0.3538.77 Safari/537.36&quot; etag=&quot;RYk0ArWTLwdiHZTnkmIy&quot; version=&quot;10.0.8&quot; type=&quot;onedrive&quot;&gt;&lt;diagram id=&quot;oDZVRUtpELmo-aTETjTw&quot; name=&quot;Page-1&quot;&gt;jZNNb4MwDIZ/DcdJhXTtzv1ad9g0CVXbbXLBhaiBoNQU2l8/04QvdZV2AfuxnTjxG08ss/rVQJG+6xiVF0zi2hMrLwhe5gF/G3CxYDqfWZAYGVvk9yCUV3Rw4mgpYzyNEklrRbIYw0jnOUY0YmCMrsZpB63GuxaQ4B0II1D39EvGlDo6e572gS3KJHVbB0K4A2bQZrujnFKIdTVAYu2JpdGarJXVS1TN5bUXY+s2D6JdZwZz+k9BsE1qgGtyrH6+SU938+3b+smN5wyqdCf2gpni9RanAnK2k8b+BJLNLsFkhUaeMWbrA0sDzfohYcY/u5ut5SaG5S3em57cboQu7T1zMY+UnUWVSsKwgKiJVCwqZillij2fTTgVds4HWXMf3CcZfcSlVtrclhIofPCjLtKOTTA56Jz+ymz4BjKpGpXu9mVOpaOha9FvfatQf9Yd4oyGsH44E7+bND8R1BmSuXCKK5g6bbjXIZxbDaTWvoR0oLIOgpN30q3cC4ANp4HW7bV2iw1erFj/Ag==&lt;/diagram&gt;&lt;/mxfile&gt;"
    version="1.1"
    viewBox="-0.5 -0.5 113 113"
  >
    <defs />
    <ellipse
      :stroke="color"
      cx="56"
      cy="56"
      fill="#ffffff"
      stroke-width="3"
      pointer-events="none"
      rx="55"
      ry="55"
    />
    <g transform="translate(3.5,18.5)">
      <switch>
        <foreignObject
          width="104"
          height="74"
          pointer-events="all"
          requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"
          style="overflow:visible"
        >
          <div :style="'color:' + color + ';display:inline-block;font-size:16px;font-family:Ubuntu;line-height:1.2;vertical-align:top;width:104px;white-space:normal;overflow-wrap:normal;font-weight:bold;text-align:center'">
            <div
              xmlns="http://www.w3.org/1999/xhtml"
              style="display:inline-block;text-align:inherit;text-decoration:inherit"
            >
              <span>
                {{ name.substring(1, name.length - 1) }}
              </span>
              <br>
            </div>
          </div>
        </foreignObject>
        <text
          :fill="color"
          x="52"
          y="45"
          font-family="Ubuntu"
          font-size="16px"
          font-weight="bold"
          text-anchor="middle"
        >
          {{ name.substring(1, name.length - 1) }}
        </text>
      </switch>
    </g>
  </svg>
</template>

<script>
export default {
  props: {
    name: {
      default: 'Cell Line Name',
      type: String
    }
  },
  data: () => ({}),
  computed: {
    color: function() {
      switch (this.name) {
        case '[Primary Tumour Tissue]':
          return '#a6cee3'
        case '[GBM Cell Line]':
          return '#1f78b4'
        case '[Engineered GBM Cell Line]':
          return '#b2df8a'
        case '[Xenograph Derive Cell Line]':
          return '#33a02c'
        case '[Peripheral Blood Mononuclear Cell]':
          return '#fb9a99'
        case '[Patient Derived Neural Stem Cell]':
          return '#e31a1c'
        case '[Human Feotal Primary Tissue]':
          return '#fdbf6f'
        case '[Human Neural Stem Cell Line]':
          return '#ff7f00'
        case '[Isogenic Gene Cell Line]':
          return '#cab2d6'
      }
    }
  }
}
</script>
