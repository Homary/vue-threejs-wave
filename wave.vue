<!-- 背景3D波浪 -->

<template>
<div class="bg-box">
</div>
</template>

<script type="text/ecmascript-6">
// import eventBus from '@static/js/eventBus';
import { THREE } from './Three';

export default{
    name: 'waves',
    data: function() {
        return {
            AMOUNTX : 50,
            AMOUNTY : 50,
            particles: null, 
            particle: 0,
            count: 0,
            camera: null,
            scene: null,
            renderer: null,
            mouseX : 180,
            mouseY : -360,
            windowHalfX : window.innerWidth / 2,
            windowHalfY : window.innerHeight / 2,
            container: null
        }
    },
    mounted: function() {       
        this.init();
        this._timer = setInterval(this.loop, 1000 / 60);
    },
    methods: {
        init: function() {
            this.container = document.createElement( 'div' );
            this.container.style.position = "absolute";
            this.container.style.left = "-20px";
            this.container.style.bottom = "30px";
            this.container.style.zIndex = 99;
            document.body.appendChild( this.container );

            let SCREEN_WIDTH = window.innerWidth,
                SCREEN_HEIGHT = window.innerHeight,
                SEPARATION = 100;

            this.camera = new THREE.Camera( 75, SCREEN_WIDTH / SCREEN_HEIGHT, 1, 10000 );
            this.camera.position.z = 999;

            this.scene = new THREE.Scene();

            this.renderer = new THREE.CanvasRenderer();
            this.renderer.setSize( SCREEN_WIDTH, SCREEN_HEIGHT/5*3);

            this.particles = new Array();

            var i = 0;

            // 修改波浪颜色
            var material = new THREE.ParticleCircleMaterial( 0xffffff, 0.85);

            for ( var ix = 0; ix < this.AMOUNTX; ix ++ ) {

                for ( var iy = 0; iy < this.AMOUNTY; iy ++ ) {
                    
                    this.particle = this.particles[ i ++ ] = new THREE.Particle( material );
                    this.particle.position.x = ix * SEPARATION - ( ( this.AMOUNTX * SEPARATION ) / 2 );
                    this.particle.position.z = iy * SEPARATION - ( ( this.AMOUNTY * SEPARATION ) / 2 );
                    this.scene.add( this.particle );
                }
            }

            this.count = 0;
            this.container.appendChild( this.renderer.domElement );

            document.addEventListener( 'mousemove', this.onDocumentMouseMove, false );
            //document.addEventListener( 'touchstart', this.onDocumentTouchStart, false );
            //document.addEventListener( 'touchmove', this.onDocumentTouchMove, false );
            window.addEventListener( 'resize', this.onWindowResize, false );

            // 销毁
            //eventBus.$on('parentRouteChange', this.handleEventBus);
        },
        handleEventBus: function(boo){
            clearInterval(this._timer);
            
            if(this.container){
                this.container.parentNode.removeChild(this.container);
                this.container = null;
            }

            return;
        },
        onWindowResize: function() {
            let SCREEN_WIDTH = window.innerWidth,
                SCREEN_HEIGHT = window.innerHeight;

            this.renderer.setSize( SCREEN_WIDTH, SCREEN_HEIGHT/5*3);
        },
        //
        onDocumentMouseMove: function( event ) {
            this.mouseX = event.clientX - this.windowHalfX;
            //this.mouseY = event.clientY - this.windowHalfY;
        },
        onDocumentTouchStart: function( event ) {

            if ( event.touches.length == 1 ) {
                event.preventDefault();

                this.mouseX = event.touches[ 0 ].pageX - this.windowHalfX;
                //this.mouseY = event.touches[ 0 ].pageY - this.windowHalfY;
            }
        },
        onDocumentTouchMove: function( event ) {

            if ( event.touches.length == 1 ) {

                event.preventDefault();

                this.mouseX = event.touches[ 0 ].pageX - this.windowHalfX;
                //this.mouseY = event.touches[ 0 ].pageY - this.windowHalfY;
            }
        },
        //
        loop: function() {
            this.camera.position.x += ( this.mouseX - this.camera.position.x ) * .05;
            this.camera.position.y += ( - this.mouseY - this.camera.position.y ) * .05;

            var i = 0;

            for ( var ix = 0; ix < this.AMOUNTX; ix ++ ) {
                for ( var iy = 0; iy < this.AMOUNTY; iy ++ ) {
                    this.particle = this.particles[ i++ ];
                    this.particle.position.y = ( Math.sin( ( ix + this.count ) * 0.3 ) * 50 ) + ( Math.sin( ( iy + this.count ) * 0.5 ) * 50 );
                    this.particle.scale.x = this.particle.scale.y = ( Math.sin( ( ix + this.count ) * 0.3 ) + 1 ) * 2 + ( Math.sin( ( iy + this.count ) * 0.5 ) + 1 ) * 2;
                }
            }
            this.renderer.render( this.scene, this.camera );
            this.count += 0.1;
        }
    }
}
</script>
<style rel="stylesheet/less" lang="less" scoped>
.bg-box{
    position: absolute;
    top: 0;
    left: 0;
    bottom: 0;
    right: 0;
    margin: auto;
    height: 100%;
    background-size: 100%;
    text-align: center;
    margin: 0px;
    overflow: hidden;
}
</style>