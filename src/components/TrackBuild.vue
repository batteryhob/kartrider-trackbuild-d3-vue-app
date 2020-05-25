<template>
<transition name="modal">

    <!--buildview-->
    <div id="buildview">
        <div id="wrapper">
            <div id="container">
                <a href="javascript:;" @click="closeModal"><img src="/img/icon_close_small.png" alt="닫기"></a>
                <div class="title">
                    배터리호님의<span class="blue"> 주간 베스트 빌드</span>
                    <div class="track">
                        빌리지 고가의 질주
                        <img src="https://s3-ap-northeast-1.amazonaws.com/solution-userstats/kartimg/Category/village_1.png">
                    </div>
                </div>
                <div class="build">
                    <div class="svg">
                        <!--트랙빌드-->
                    </div>
                    <div id="pointdesc">
                        <!--메세지-->                        
                    </div>
                </div>
                <img src="/img/lab_logo.svg" style="
                    position:absolute;
                    top: 50%;
                    left: 50%;
                    margin-top: -23px;
                    margin-left: -70px;
                    opacity: 0.2; 
                    width: 140px; z-index: -1;">
                <div class="remote">
                    <div class="desc">
                        <ul>
                            <li><i class="fas fa-map-marker-alt start"></i>스타팅포인트</li>  
                            <li><i class="fas fa-circle boost"></i>부스터주행</li>  
                            <li><i class="fas fa-circle normal"></i>일반주행</li> 
                            <li><i class="fas fa-circle drift"></i>드리프트</li>
                        </ul>
                    </div>
                    <div class="buttons">
                        <button id="plus"><i class="fas fa-plus"></i></button>
                        <button id="minus"><i class="fas fa-minus"></i></button>
                    </div>
                </div>
            </div>
        </div>
    </div>
    <!--buildview-->

</transition>
</template>

<script>

import response from '../assets/data/data'
import $ from 'jquery'
import * as d3 from "d3"
//import * as d3Zoom from 'd3-zoom'

export default {
    name: 'TrackBuild',
    props: [ 'matchtype'],
    components: {

    },
    computed: {

    },
    mounted(){

        $(document).mousemove(function(e) {
            var mouseX = e.pageX;
            var mouseY = e.pageY - window.scrollY;
            $('#pointdesc').css("left", mouseX+15).css("top", mouseY+15);
        });
        
        function msToHMS(ms){
            function pad(n, width) {
                n = n + '';
                return n.length >= width ? n : new Array(width - n.length + 1).join('0') + n;
            }

            if(ms == '' || ms == null)
                return '-';

            // 1- Convert to seconds:
            var seconds = ms / 1000;
            // 2- Extract hours:
            seconds = seconds % 3600; // seconds remaining after extracting hours
            // 3- Extract minutes:
            var minutes = parseInt( seconds / 60 ); // 60 seconds in 1 minute
            // 4- Keep only seconds not extracted to minutes:

            var se = pad(parseInt(seconds % 60), 2)            
            var mse = pad((((seconds % 60) - parseInt(seconds % 60)) * 100).toFixed(0),2)

            return `${minutes}'${se}'${mse}`;
        }

        let x_min = 0;
        let x_max = 0;
        let y_min = 0;
        let y_max = 0;

        if(!response.data.result)
        {
            alert('데이터가 없습니다.')
            return
        }

        var shape = [];
        var path = [];

        x_min = response.data.shapeData.x_min;
        x_max = response.data.shapeData.x_max;
        y_min = response.data.shapeData.y_min;
        y_max = response.data.shapeData.y_max;

        response.data.shapeData.shape.forEach(element => {

            let x = element.split('|')[0];
            let y = element.split('|')[1];
            let pt = {
            x: x,
            y: y,
            drift: '-1',
            boost: '-1',
            tick: '-1'
            }
            shape.push(pt)

        });

        response.data.pathData.path.forEach(element => {

            //x,y,drift,boost,tick
            let x = element.split('|')[0];
            let y = element.split('|')[1];
            let drift = element.split('|')[2];
            let boost = element.split('|')[3];
            let tick = element.split('|')[4];
            let speed = element.split('|')[5];

            let pt = {
                x: x,
                y: y,
                drift: drift,
                boost: boost,
                tick: tick,
                speed: speed
            };

            path.push(pt)
            path.sort((a,b)=>{
                return a.tick - b.tick;
            })

        });

        var data = [...shape,...path];

        this.$data.x_max = x_max-x_min;
        this.$data.y_max = y_max-y_min;

        //0.scale
        var x_scale = d3.scaleLinear().domain([x_min, x_max]).range([0, x_max-x_min]);
        var y_scale = d3.scaleLinear().domain([y_min, y_max]).range([0, y_max-y_min]);

        //1.circle
        var csvg = d3.select(".svg")
        .append("svg")
        .attr("class", "d3area")
        .attr("width", this.$data.x_max)
        .attr("height", this.$data.y_max)
        

        var circle = csvg.selectAll("circle") 
        .data(data)
        .enter()
        .append("circle")
        .style("fill", function(d) {
            if(d.drift === '-1') return "rgba( 11, 42, 74, 1)"
            if(d.drift === '1') return "rgba( 248, 102, 132, 1)"
            if(d.boost === '1') return "rgba( 0, 119, 255, 1)"
            else return 'rgba( 0, 119, 255, 0.2)'
        ;})
        .on("mouseover", function(d) {		

            if(d.tick == '-1')
                return

            let boost = d.boost == '1' ? 'O' : 'X';
            let drift = d.drift == '1' ? 'O' : 'X';

            $('#pointdesc').css('opacity', 1).html(
                `
                <ul>
                    <li>부스터 사용 :&nbsp;&nbsp;${boost}</li>
                    <li>드리프트 사용 :&nbsp;&nbsp;${drift}</li>
                    <li>랩타임 : &nbsp;&nbsp;${msToHMS(d.tick)}</li>
                    <li>속도 : &nbsp;&nbsp;${parseFloat(d.speed).toFixed(2)}&nbsp;km/h</li>
                </ul>                    
                `
            )
        })					
        .on("mouseout", function() {		
            $('#pointdesc').css('opacity', 0).html()
        })            
        .attr("cx", function(d) { return x_scale(d.x) })
        .attr("cy", function(d) { return y_scale(d.y) })
        .attr("r", 2.5)

        //2.line
        // var line = d3.line()
        //         .x(function(d) { return x_scale(d.x) })
        //         .y(function(d) { return y_scale(d.y) });
    
        // csvg.append("path")
        // .attr("d", line(path))
        // .attr("stroke", "#0077FF")
        // .attr("stroke-width", 1)
        // .attr("fill", "none")
        // .attr("class", "line" );
    
        //3.grid
        // Gridline
        var x_grid = d3.axisTop()
                            .tickFormat("")
                            .tickSize(-y_max)
                            .scale(x_scale);

        var y_grid = d3.axisLeft()
                            .tickFormat("")
                            .tickSize(-x_max)
                            .scale(y_scale);

        csvg.append("g")
            .attr("class", "d3grid")
            .call(x_grid);

        csvg.append("g")
            .attr("class", "d3grid")
            .call(y_grid);

        //4.start

        // eslint-disable-next-line
        var mark = csvg.append('svg:foreignObject').attr("opacity", 0)
            .html('<i class="fas fa-map-marker-alt"></i>').style("color", "rgba( 155, 215, 40, 1)")
            .transition().duration(1000)
            .attr("class", "mark")                
            .attr('x', x_scale(path[0].x))
            .attr('y', y_scale(path[0].y))
            .attr("font-size", 25)                
            .attr("width", 30)
            .attr("height", 35)
            .attr("opacity", 1)
            .style("transform", "translate(-17px, -32.5px)")              
            
            
        $('#plus').on('click',()=>{                
            
            if(this.$data.scaleValue < 2)
                this.$data.scaleValue = +(this.$data.scaleValue+0.1).toFixed(12);

            var x_diff = parseFloat(this.$data.x_max * this.$data.scaleValue).toFixed(1)
            var x_margin = parseFloat(x_diff - this.$data.x_max).toFixed(1)
            var y_diff = parseFloat(this.$data.y_max * this.$data.scaleValue).toFixed(1)
            var y_margin = parseFloat(y_diff - this.$data.y_max).toFixed(1)

            csvg.transition().duration(1000)
            .attr("width", x_diff)
            .attr("height", y_diff)
            .attr("transform", `scale(${this.$data.scaleValue})`)
            circle.transition().duration(1000).attr("transform", `translate(${x_margin}, ${y_margin})`)
            $('.mark').attr("x", `${(parseFloat(x_margin) + parseFloat(x_scale(path[0].x))).toFixed(1)}`)
            $('.mark').attr("y", `${(parseFloat(y_margin) + parseFloat(y_scale(path[0].y))).toFixed(1)}`)
            $('.d3grid').attr("transform", `translate(${x_margin}, ${y_margin})`)     


        })
        $('#minus').on('click',()=>{
            
            if(this.$data.scaleValue > 1)
                this.$data.scaleValue = +(this.$data.scaleValue-0.1).toFixed(12);
            
            var x_diff = parseFloat(this.$data.x_max * this.$data.scaleValue).toFixed(1)
            var x_margin = parseFloat(x_diff - this.$data.x_max).toFixed(1)
            var y_diff = parseFloat(this.$data.y_max * this.$data.scaleValue).toFixed(1)
            var y_margin = parseFloat(y_diff - this.$data.y_max).toFixed(1)

            csvg.transition().duration(1000)
            .attr("width", x_diff)
            .attr("height", y_diff)
            .attr("transform", `scale(${this.$data.scaleValue})`)
            circle.transition().duration(1000).attr("transform", `translate(${x_margin}, ${y_margin})`)
            $('.mark').attr("x", `${(parseFloat(x_margin) + parseFloat(x_scale(path[0].x))).toFixed(1)}`)
            $('.mark').attr("y", `${(parseFloat(y_margin) + parseFloat(y_scale(path[0].y))).toFixed(1)}`)
            $('.d3grid').attr("transform", `translate(${x_margin}, ${y_margin})`) 

        })

    },
    methods: {
        closeModal(){
            this.$parent.$data.modalBuild = false
        }
    },
    data() {
        return {
            scaleValue: 1.0,
            x_max: 0,
            y_max: 0,
        }
    }
}
</script>

<style>
.d3area {
    border-right: 1px solid #ccc;
    border-bottom: 1px solid #ccc
}

.d3grid line{
  stroke: #ccc;
}

.d3grid path{
  stroke: #ccc;
}

.modal-enter {
  opacity: 0;
}

.modal-leave-active {
  opacity: 0;
}

.modal-enter .modal-container,
.modal-leave-active .modal-container {
  -webkit-transform: scale(1.1);
  transform: scale(1.1);
}

</style>