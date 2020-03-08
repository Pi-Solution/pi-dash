<template>
    <div id="pi-dash" class="pi-dash"
        @mousemove="dragWidget"
        @mouseup="stopWidgetDragging"

    >
        <GhostItem
            v-for="index in 160"
            :key="'gi' + index"
            :getChosenPosition="getChosenPosition"
            :pAtt="{
            row: Math.ceil((index - 0.25) / 16),
            column: index % 16 + 1
            }"
        />
        <list
            v-for="(widget, index) in widgets.list"
            :key="'list' + widget.id"
            :id ="'list' + widget.id"
            :array_index="index"
            :widget="widget"
            :getRelativeCoordinates="getRelativeCoordinates"
            :startWidgetMoving="startWidgetMoving"
        />
        <info
            v-for="widget in widgets.info"
            :key="'info' + widget.id"
            :widget="widget"
        />
        <other
            v-for="widget in widgets.other"
            :key="'other' + widget.id"
            :widget="widget"
        />
    </div>
</template>

<script>
    import GhostItem from "./pi-dash/GhostItem";
    import list from "./pi-dash/Widgets/list";
    import info from "./pi-dash/Widgets/info";
    import other from "./pi-dash/Widgets/other";

    export default {
        name: "pi-dash",
        components:{
            GhostItem,
            list,
            info,
            other
        },
        data(){
            return {
                // This is where all your widgets are stored
                widgets: Object,
                // This is where position is stored that is used when you drag item to save it to new position
                dragToThisPosition: Object,
                // This array is used to si if widget dragging is activated and to store info which widget to move
                WidgetDragAndDrop: {
                    dragWidget: null,
                    componentName: null,
                    array_index: null,
                    mousePositionInWidget: {
                        top: null,
                        left: null
                    },
                    mouseCalculatedGridPosition: null
                }
            }
        },
        methods:{
            getChosenPosition(position){
                this.dragToThisPositiona = position;
            },
            startWidgetMoving(component_name, array_index, widgetMousePosition, mouseCalculatedGridPosition){
                this.WidgetDragAndDrop.dragWidget = true;
                this.WidgetDragAndDrop.componentName = component_name;
                this.WidgetDragAndDrop.array_index = array_index;
                this.WidgetDragAndDrop.mousePositionInWidget.left = widgetMousePosition.x;
                this.WidgetDragAndDrop.mousePositionInWidget.top = widgetMousePosition.y;
                this.WidgetDragAndDrop.mouseCalculatedGridPosition = mouseCalculatedGridPosition;
            },
            // This function is triggered every time mouse is moved inside dashboard and if some widget is set to move
            // it will drag widget with mouse
            dragWidget(){
                if (this.WidgetDragAndDrop.dragWidget){
                    let dashboard = document.getElementById('pi-dash');
                    let mousePositionDashboard = this.getRelativeCoordinates(dashboard);
                    //console.log(mousePositionDashboard);
                    //console.log(this.widgets[this.WidgetDragAndDrop.componentName][this.WidgetDragAndDrop.array_index]);
                    this.widgets[this.WidgetDragAndDrop.componentName][this.WidgetDragAndDrop.array_index].position.absolute.top = mousePositionDashboard.y - this.WidgetDragAndDrop.mousePositionInWidget.top + "px";
                    this.widgets[this.WidgetDragAndDrop.componentName][this.WidgetDragAndDrop.array_index].position.absolute.left = mousePositionDashboard.x - this.WidgetDragAndDrop.mousePositionInWidget.left + "px";
                }
            },
            // This function is triggered when you release mouse click "mouseup" and it gets location of where widget should now be located form  and it sends it to widget
            stopWidgetDragging(){
                //console.log(this.dragToThisPositiona);
                if (this.WidgetDragAndDrop.dragWidget){
                    this.widgets[this.WidgetDragAndDrop.componentName][this.WidgetDragAndDrop.array_index].position.absolute.top = "";
                    this.widgets[this.WidgetDragAndDrop.componentName][this.WidgetDragAndDrop.array_index].position.absolute.left = "";
                    this.widgets[this.WidgetDragAndDrop.componentName][this.WidgetDragAndDrop.array_index].position.position = "relative";
                    this.widgets[this.WidgetDragAndDrop.componentName][this.WidgetDragAndDrop.array_index].position.row = this.dragToThisPositiona.row - this.WidgetDragAndDrop.mouseCalculatedGridPosition.row;
                    this.widgets[this.WidgetDragAndDrop.componentName][this.WidgetDragAndDrop.array_index].position.column = this.dragToThisPositiona.column - this.WidgetDragAndDrop.mouseCalculatedGridPosition.column;
                    this.widgets[this.WidgetDragAndDrop.componentName][this.WidgetDragAndDrop.array_index].pointerEvents = "all";
                    this.widgets[this.WidgetDragAndDrop.componentName][this.WidgetDragAndDrop.array_index].glow = "none";
                    //console.log(this.widgets[this.WidgetDragAndDrop.componentName][this.WidgetDragAndDrop.array_index])
                    this.WidgetDragAndDrop.dragWidget = null;
                    this.WidgetDragAndDrop.componentName = null;
                    this.WidgetDragAndDrop.array_index = null;
                }
            },
            //Get mouse position in given div
            getRelativeCoordinates(referenceElement) {

                const position = {
                    x: event.pageX,
                    y: event.pageY
                };

                const offset = {
                    left: referenceElement.offsetLeft,
                    top: referenceElement.offsetTop
                };

                let reference = referenceElement.offsetParent;

                while(reference){
                    offset.left += reference.offsetLeft;
                    offset.top += reference.offsetTop;
                    reference = reference.offsetParent;
                }

                return {
                    x: position.x - offset.left,
                    y: position.y - offset.top,
                };
            }
        },
        /**
         * When this Component is created created function loads all widgets from API or some other source that you choose
         * and saves it to the Widget Array
         */
        created() {
            const res = [
                {
                    id: 1,
                    component: "list",
                    position: {
                        row: 1,
                        column: 4
                    },
                    size: {
                        width: 2,
                        height: 2
                    },
                    data: {
                        somedata: "data"
                    }
                },
                {
                    id: 2,
                    component: "list",
                    position: {
                        row: 1,
                        column: 1
                    },
                    size: {
                        width: 1,
                        height: 3
                    },
                    data: {
                        somedata: "data"
                    }
                },
                {
                    id: 3,
                    component: "info",
                    position: {
                        row: 4,
                        column: 1
                    },
                    size: {
                        width: 3,
                        height: 3
                    },
                    data: {
                        somedata: "data"
                    }
                },
                {
                    id: 4,
                    component: "info",
                    position: {
                        row: 7,
                        column: 1
                    },
                    size: {
                        width: 1,
                        height: 3
                    },
                    data: {
                        somedata: "data"
                    }
                },
                {
                    id: 5,
                    component: "other",
                    position: {
                        row: 1,
                        column: 2
                    },
                    size: {
                        width: 2,
                        height: 2
                    },
                    data: {
                        somedata: "data"
                    }
                }
            ];
            // Sort all component by category and put them to different arrays
            let widgets = new Object();

            res.forEach(widget => {
                widget.position.position = "relative";
                widget.position.absolute = {
                    top: "",
                    left: ""
                };
                widget.size.absolute = {
                    width: "",
                    height: ""
                };
                widget.user_select = 'auto';
                widget.pointerEvents = 'all';
                widget.glow = "none";

                // eslint-disable-next-line no-prototype-builtins
                if (!widgets.hasOwnProperty(widget.component)){
                    widgets[widget.component] = new Array();
                    widgets[widget.component].push(widget)
                }else {
                    widgets[widget.component].push(widget)
                }
            });
            this.widgets = widgets
        }
    }
</script>

<style scoped>
    .pi-dash{
        position: absolute;
        left: 50px;
        right: 50px;
        top: 50px;
        padding: 0.5em;
        bottom: 50px;
        border: 1px solid black;
        box-sizing: border-box;

        display: grid;
        grid-template-columns: repeat(16, 1fr);
        grid-template-rows: repeat(10, 1fr);
        grid-gap: 0.5em;

        -webkit-user-select: none; /* Safari */
        -khtml-user-select: none; /* Konqueror HTML */
        -moz-user-select: none; /* Old versions of Firefox */
        -ms-user-select: none; /* Internet Explorer/Edge */
        user-select: none;
    }
</style>