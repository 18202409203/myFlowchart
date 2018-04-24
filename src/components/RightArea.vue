<template>
  <div class="right-area" ref="right">
    <el-dialog :title="`请${dialogTitle}`" :visible="dialogVisible">
      <el-input v-model="dialogText" auto-complete="off"></el-input>
      <div slot="footer" class="dialog-footer">
        <el-button type="danger" @click="remove" v-show="dialogTitle.startsWith('编辑')">
          删除{{dialogTitle.substr(2,2)}}
        </el-button>
        <el-button @click="closeDialog">取 消</el-button>
        <el-button type="primary" @click="summit">确 定</el-button>        
      </div>
    </el-dialog>
    <div v-if="nodes.length"
    v-for="node in nodes"
    class="node"
    :class="node.className"
    :id="node.id"
    ref="nodes"
    :style="node.style"
    @dblclick="editNodeText(node)">{{ node.text }}</div>
    <!-- <el-button class="demo" type="primary" @click="getAllNodes">获取所有节点</el-button> -->
    <!-- <el-button class="demo" type="primary" @click="getAlledges">获取所有连接</el-button> -->
    <div  class="demo">
      <el-button  @click="saveDatas">保存</el-button>
      <el-button  @click="clearAll">清除</el-button>
      <el-button  @click="reload">重现</el-button>
      <el-tooltip content="显示端点以连线或隐藏端点" placement="bottom">
        <el-button  @click="hideEndpoints">连线</el-button>
      </el-tooltip>
    </div>
    <div class="radio">
      <el-radio-group v-model="radio2" @change="changeEndPointStyle">
        <el-radio :label="1">四点</el-radio>
        <el-radio :label="2">八点</el-radio>
        <!--el-radio :label="3">备选项</el-radio>
        <el-radio :label="4">备选项</el-radio-->
      </el-radio-group>
    </div>
  </div>
</template>

<script>
 

  const connectorStyle = { strokeWidth: 2, stroke: '#445566', joinstyle: 'round' }

  const connectorHoverStyle = { stroke: '#ec9f2e', strokeWidth: 4 }

  const endpointStyle = { radius: 6, fill: 'gray' }

  const endpointHoverStyle = { fill: '#ec9f2e', stroke: '#acd' }


  // 通用源连接点
  /*    var sourceEndpoint = {
        endpoint: ["Dot", { radius:7 }],
        paintStyle: {stroke: "#7AB02C", fill: color},
        maxConnections: -1,
        isSource: true,
      }
      // 特例源连接点
      var sourceEndpoint_special = {
        endpoint: ["Dot", { radius:7 }],
        paintStyle: {stroke: "#7AB02C", fill: color},
        maxConnections: -1,
        connector: ['Flowchart', { stub: [160, 50], gap: 7, cornerRadius: 5, alwaysRespectStubs: true }],
        isSource: true,
      }
      // 目标连接点
      var targetEndpoint = {
        endpoint: "Dot",
        paintStyle: { fill: color, radius: 7 },
        maxConnections: -1,
        dropOptions: { hoverClass: "hover", activeClass: "active" },
        isTarget: true,
      } */

export default {
    name: 'RightArea',
    props: {
      jsp: {
        type: Object
      },
      pos: {
        type: Array
      }
    },
    data () {
      return {
        datas: null,
        dialogVisible: false,
        dialogTitle: '',
        dialogText: '',
        nodes: [],
        info: null,
        radio2: 1,
        rjsp: this.$jsplumb.getInstance({
          ConnectionOverlays: [
          ['Arrow', { location: 1, id: 'arrow', width: 11, length: 11 }],
          ['Label', { location: 0.3, id: 'label', cssClass: 'jsp-label', events: {dblclick: this.editLabelText} }]
          ]
        }),
        DynamicAnchors: ['Left', 'Right', 'Top', 'Bottom'], /* 'TopRight', 'BottomRight', 'TopLeft', 'BottomLeft' */
        anEndpoint: {
          connector: 'Flowchart',
          endpoint: 'Blank',
          isSource: true,
          isTarget: true,
          paintStyle: endpointStyle,
          hoverPaintStyle: endpointHoverStyle,
          connectorStyle: connectorStyle,
          connectorHoverStyle: connectorHoverStyle
        },
        Common: {
          anchor: 'AutoDefault',
          connector: ['Flowchart', { gap: 0, cornerRadius: 5, alwaysRespectStubs: true }],
          endpoint: 'Dot',
          paintStyle: connectorStyle,
          hoverPaintStyle: connectorHoverStyle,
          endpointStyle,
          endpointHoverStyle
        },
        hideEndpointsFlag: true
      }
    },
    methods: {
      changeEndPointStyle () {
          if(this.radio2 == '1'){
            this.DynamicAnchors = ['Left', 'Right', 'Top', 'Bottom']
          }else if(this.radio2 == '2'){
            this.DynamicAnchors = ['Left', 'Right', 'Top', 'Bottom', 'TopRight', 'BottomRight', 'TopLeft', 'BottomLeft']
          }
          this.saveDatas()
          this.reload()
      },
      hideEndpoints () {
        this.hideEndpointsFlag = !this.hideEndpointsFlag
          if(this.hideEndpointsFlag == true)
          {
            this.anEndpoint.endpoint = 'Blank'
          }
          else{
            this.anEndpoint.endpoint = 'Dot'
          }
        this.saveDatas()
        this.reload()
      },
      init () {
        this.jsp.droppable(this.$refs.right, { drop: this._jspDrop })
        this.rjsp.bind('beforeDrop', this._jspBeforeDrop)
        this._fetchData()
      },
      _jspDrop (info) {
        this.info = info
        this.openDialog('输入新建节点的文本')
      },
      _jspBeforeDrop (info) {
        info.targetId = info.dropEndpoint.elementId
        let connections = this.rjsp.getConnections({ source: info.sourceId, target: info.targetId })
        if (connections.length === 0) {  // 检察是否已经建立过连接
          this.info = info
          this.openDialog('输入新建连接的文本')
        } else {
          this.editLabelText(connections[0].getOverlay('label'))
        }
      },
      openDialog (title) {
        this.dialogTitle = title
        this.dialogVisible = true
      },
      closeDialog () {
        this.dialogTitle = ''
        this.dialogText = ''
        this.dialogVisible = false
      },
      editNodeText (info) {
        this.dialogText = info.text
        this.info = info
        this.openDialog('编辑节点的文本')
      },
      editLabelText (info) {
        this.dialogText = info.labelText
        this.info = info.component
        this.openDialog('编辑连接的文本')
      },
      summit () {
        if (this.dialogTitle === '输入新建节点的文本') {
          this.nodes.push(this._createNode(this.info.drag.el, this.info.drop.el))
          this.$nextTick(_ => this._initNode(this.$refs.nodes[this.nodes.length - 1]))
        } else if (this.dialogTitle === '编辑节点的文本') {
          this.info.text = this.dialogText
        } else if (this.dialogTitle === '输入新建连接的文本') {
          console.log(this.info)
          this._addEdge(this.info)
        } else if (this.dialogTitle === '编辑连接的文本') {
          let labelText = this.dialogText
          this.$nextTick(_ => (this.info.getOverlay('label').setLabel(labelText)))
        }
        this.closeDialog()
      },
      saveDatas () {
        this.datas = {
          nodes: this.nodes.map((node, index) => {
            node.style = this._getStyle(this.$refs.nodes[index])
            return node
          }),
          edges: this.rjsp.getAllConnections().map(connection => {
            return {
              source: connection.sourceId,
              target: connection.targetId,
              labelText: connection.getOverlay('label').labelText
            }
          })
        }
        // console.log(JSON.stringify(this.datas))
      },
      _getStyle (node) {
        let container = this.$refs.right.getBoundingClientRect()
        let rect = node.getBoundingClientRect()
        return {
          left: rect.left - container.left - node.clientLeft + 'px',
          top: rect.top - container.top - node.clientTop + 'px',
          lineHeight: node.clientHeight + 'px'
        }
      },
      clearAll () {
        this.rjsp.reset()
        this.nodes = []
      },
      reload () {
        this.clearAll()
        this.rjsp.bind('beforeDrop', this._jspBeforeDrop)
        this.nodes = this.datas.nodes
        this.$nextTick(_ => {
          this._initNodes(this.$refs.nodes)
          this._initEdges(this.datas.edges)
        })
      },
      remove () {
        if (this.dialogTitle === '编辑节点的文本') {
          this._removeNode(this.info)
        } else if (this.dialogTitle === '编辑连接的文本') {
          this._removeEgde(this.info)
        }
        this.closeDialog()
      },
      _removeNode (node) {
        this.rjsp.deleteConnectionsForElement(node.id)
        this.saveDatas()
        this.datas.nodes.splice(this.datas.nodes.findIndex(n => n.id === node.id), 1)
        this.reload()
      },
      _removeEgde (egde) {
        this.rjsp.deleteConnection(egde)
        this.saveDatas()
      },
      _addEdge (info) {
        let labelText = this.dialogText
        let c = this.rjsp.connect({ source: info.sourceId, target: info.targetId }, this.Common)
        this.$nextTick(_ => c.getOverlay('label').setLabel(labelText))
      },
      _createNode (dragEl, dropEl) {
        let rect = dropEl.getBoundingClientRect()
        return {
          className: dragEl.classList[0],
          id: this.$util.uuid(),
          text: this.dialogText,
          style: {
            left: this.pos[0] - rect.left - dragEl.clientLeft + 'px',
            top: this.pos[1] - rect.top - dragEl.clientTop + 'px',
            lineHeight: dragEl.clientHeight + 'px'
          }
        }
      },
      _initNode (node) {
        this.rjsp.draggable(node, { constrain: true })
        this.DynamicAnchors.map(anchor => this.rjsp.addEndpoint(node, this.anEndpoint, { anchor }))
        this.rjsp.makeTarget(node, { allowLoopback: false })
      },
      _initNodes (nodes) {
        nodes.map(node => this._initNode(node))
      },
      _initEdges (edges) {
        edges.map(edge => this.rjsp.connect(edge, this.Common).getOverlay('label').setLabel(edge.labelText))
      },
      _fetchData () {
        this.$http.get('/api/data').then(res => {
          this.nodes = res.data.nodes
          this.$nextTick(_ => {
            this._initNodes(this.$refs.nodes)
            this._initEdges(res.data.edges)
          })
        }).catch(err => console.log(err))
      }
    },
    mounted () {
      this.init()
    }
  }
</script>

<style lang="css" scoped>
  .right-area {
    position: relative;
  }

  .demo {
    position: fixed;
    border:1px solid black;
    width:100%;
  }

  .node {
    padding: 0 10px;
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
    font-size: 14px;
    text-align: center;
    cursor: pointer;
  }
  .radio{
    position: fixed;
    right: 0px;
    top: 10px;
    padding: 0 10px;
  }
</style>
