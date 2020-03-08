<template>
    <div class="pd-list"
         @mousedown="startTimeListening()"
         @mousemove="checkTimeMouseDown($event)"
         @mouseup="resetWidgetMoveData(true)"
         @mouseout="resetWidgetMoveData(false)"
         :id="id"
         :style="{
            position: widgetAttributes.position.position,
            gridRow: widgetAttributes.position.row + '/' + (widgetAttributes.position.row + widgetAttributes.size.height),
            gridColumn: widgetAttributes.position.column + '/' + (widgetAttributes.position.column + widgetAttributes.size.width),
            top: widgetAttributes.position.absolute.top,
            left: widgetAttributes.position.absolute.left,
            height: widgetAttributes.size.absolute.height,
            width: widgetAttributes.size.absolute.width,
            pointerEvents: widget.pointerEvents,
            // User Select
            webkitUserSelect: widgetAttributes.user_select,
            userSelect: widgetAttributes.user_select,
            msUserSelect: widgetAttributes.user_select,
            // Glow when widget is draggable
            outline: widgetAttributes.glow
        }"
    >
        <div class="testD"
        >
            Hello I'm
        </div>
    </div>
</template>

<script>
    export default {
        name: "list",
        props:{
            widget: Object,
            id: String,
            array_index: Number,
            getRelativeCoordinates: Function,
            startWidgetMoving: Function
        },
        data(){
            return {
                widgetAttributes: Object,
                trackMouseDownTime: false,
                timeMouseDown: Number,
                firstMove: true
            }
        },
        methods:{
            /**
             * When you ClickDown on widget this method saves current time to variable "timeMouseDown" and
             * sets "trackMouseDownTime" variable to true to when method "checkTimeMouseDown" that checks if
             * the "trackMouseDownTime" is true
            */
            startTimeListening(){
                this.trackMouseDownTime = true;
                const date = new Date();
                const time = date.getTime();
                this.timeMouseDown = time;
                this.addGlowToWidget()
            },
            /**
             * This methods check if "trackMouseDownTime" is true and if that is the case it checks if mouse is
             * already done some moving inside div (if user for some reason moves clicked mouse thor to marker
             * something or to do some other operation it prevents widget movement for some time).
             * If those condition are meet method checks if move was done after given time. If move was done after
             * given time the method for dragging is activated else "firstMove" is set to false so this same method
             * knows that user wanted to something else to do instead of dragging widget
            */
            checkTimeMouseDown(event){
                if (this.trackMouseDownTime){
                    if (this.firstMove){
                        const date = new Date();
                        const time = date.getTime();
                        if (time - this.timeMouseDown > 300){
                            this.trackMouseDownTime = false;
                            //console.log(time - this.timeMouseDown);
                            this.startWidgetDragging(event);
                        }else{
                            this.firstMove = false
                        }
                    }
                }
            },
            resetWidgetMoveData(glow){
                this.trackMouseDownTime = false;
                this.firstMove = true;
                if (glow){
                    this.widgetAttributes.glow = "none"
                }
            },
            /**
             * If Widget is ready to move it will glow
            */
            addGlowToWidget(){
                setTimeout(() => {
                    if (this.trackMouseDownTime && this.firstMove){
                        this.widgetAttributes.glow = "2px solid green"
                    }
                }, 350);
            },
            /**
             *  When you "clickDown" on widget this method is triggered that calculate widget height and width in "px",
             *  calculates widget distance from top and left of dashboard and then it changes widgets position
             *  to absolute and sets size and position to calculated values. After that it fires method that sends
             *  id of widget and information that dragging is started
             */
            startWidgetDragging(event){

                // Get widget size
                let widgetAbsoluteWidth = getComputedStyle(event.currentTarget, null).width;
                let widgetAbsoluteHeight = getComputedStyle(event.currentTarget, null).height;
                // Get mouse position inside widget
                let widgetMousePosition = this.getRelativeCoordinates(event.currentTarget);

                /* This Object will store calculated position for cursor in div. It will be used so we can determinate
                *  how to place widget after dragging
                */
                let mousePositionInDivGrid = new Object();
                // Convert width and height prom string to number
                let convertedWidth = parseFloat(widgetAbsoluteWidth.substring(0, widgetAbsoluteWidth.length - 2));
                let convertedHeight = parseFloat(widgetAbsoluteHeight.substring(0, widgetAbsoluteHeight.length - 2));
                mousePositionInDivGrid.row = Math.floor(widgetMousePosition.y / (convertedHeight / this.widgetAttributes.size.height));
                mousePositionInDivGrid.column = Math.floor(widgetMousePosition.x / (convertedWidth / this.widgetAttributes.size.width));
                // Get position on cursor inside dasboard
                let dashboard = document.getElementById('pi-dash');
                let dashboardMousePosition = this.getRelativeCoordinates(dashboard);

                // Set div position to absolute and set other needed absolute attributes
                this.widgetAttributes.position.position = "absolute";

                this.widgetAttributes.size.absolute.width = widgetAbsoluteWidth;
                this.widgetAttributes.size.absolute.height = widgetAbsoluteHeight;
                this.widgetAttributes.position.absolute.left = dashboardMousePosition.x - widgetMousePosition.x + "px";
                this.widgetAttributes.position.absolute.top = dashboardMousePosition.y - widgetMousePosition.y + "px";
                // Remove grid properties
                event.currentTarget.style.removeProperty('grid-area');
                this.widgetAttributes.position.row = "";
                this.widgetAttributes.position.column = "";
                // Remove pointer events
                this.widgetAttributes.pointerEvents = "none";
                // Send information to dashboard that this is ready for moving
                this.startWidgetMoving(this.widgetAttributes.component, this.array_index, widgetMousePosition, mousePositionInDivGrid);
            }
        },
        watch:{
            'widget.position.absolute.top'(){
                this.widgetAttributes = this.widget;
            },
            'widget.position.row'(){
                this.widgetAttributes = this.widget;
            },
            'widget.position.column'(){
                this.widgetAttributes = this.widget;
            }
        },
        created() {
            this.widgetAttributes = this.widget
        }
    }
</script>

<style scoped>
    .pd-list{
        background: cadetblue;
    }
    .dragged_item{
        outline: 2px solid #7ae7ff;
    }
    .testD{
        position: absolute;
        left: 2px;
        right: 2px;
        top: 2px;
        bottom: 2px;
        background: greenyellow;
    }
</style>