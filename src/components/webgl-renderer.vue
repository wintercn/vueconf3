<template>
  <canvas style="background-color:black;" ref="canvas" width="200px" height="200px"></canvas>
</template>

<script>
export default {
  name: 'webgl-renderer',
  props: {
    t: String
  },
  watch: { 
      t: function(newVal, oldVal) { // watch it
          if(!this.gl||!this.glProgram)
              return;
          var iTime = this.gl.getUniformLocation(this.glProgram,'iTime');
          this.gl.uniform1f(iTime, Number(newVal));
          this.gl.drawArrays(this.gl.TRIANGLE_STRIP, 0, 5);
      }
  },
  mounted: function(){
    //获取webgl上下文
    var canvas = this.$refs['canvas'];
    var gl = canvas.getContext('webgl');
    this.gl = gl;

    var vertShader = gl.createShader(gl.VERTEX_SHADER);
    gl.shaderSource(vertShader, `
    attribute vec4 a_Position;
    attribute float a_PointSize;
    void main(){
        gl_Position=a_Position;
        gl_PointSize=a_PointSize;
    }
    `);
    gl.compileShader(vertShader);

    var fragShader = gl.createShader(gl.FRAGMENT_SHADER);
    gl.shaderSource(fragShader, `
    precision mediump float;
    uniform vec4 u_FragColor;
    const mat2 m = mat2( 0.80,  0.60, -0.60,  0.80 );

    float noise( in vec2 p )
    {
      return sin(p.x)*sin(p.y);
    }

    float fbm4( vec2 p )
    {
        float f = 0.0;
        f += 0.5000*noise( p ); p = m*p*2.02;
        f += 0.2500*noise( p ); p = m*p*2.03;
        f += 0.1250*noise( p ); p = m*p*2.01;
        f += 0.0625*noise( p );
        return f/0.9375;
    }

    float fbm6( vec2 p )
    {
        float f = 0.0;
        f += 0.500000*(0.5+0.5*noise( p )); p = m*p*2.02;
        f += 0.250000*(0.5+0.5*noise( p )); p = m*p*2.03;
        f += 0.125000*(0.5+0.5*noise( p )); p = m*p*2.01;
        f += 0.062500*(0.5+0.5*noise( p )); p = m*p*2.04;
        f += 0.031250*(0.5+0.5*noise( p )); p = m*p*2.01;
        f += 0.015625*(0.5+0.5*noise( p ));
        return f/0.96875;
    }

    vec2 fbm4_2( vec2 p )
    {
        return vec2(fbm4(p), fbm4(p+vec2(7.8)));
    }

    vec2 fbm6_2( vec2 p )
    {
        return vec2(fbm6(p+vec2(16.8)), fbm6(p+vec2(11.5)));
    }
    uniform float iTime;
    vec3 iResolution = vec3(100.0, 100.0, 1.0);
    float func( vec2 q, out vec4 ron )
    {
        q += 0.03*sin( vec2(0.27,0.23)*iTime + length(q)*vec2(4.1,4.3));

      vec2 o = fbm4_2( 0.9*q );

        o += 0.04*sin( vec2(0.12,0.14)*iTime + length(o));

        vec2 n = fbm6_2( 3.0*o );

      ron = vec4( o, n );

        float f = 0.5 + 0.5*fbm4( 1.8*q + 6.0*n );

        return mix( f, f*f*f*3.5, f*abs(n.x) );
    }
    void mainImage( out vec4 fragColor, in vec2 fragCoord )
    {
        vec3 r = vec3(100.0, 100.0, 1.0);
        vec2 p = (2.0*fragCoord-r.xy)/r.y;
        float e = 2.0/r.y;

        vec4 on = vec4(0.0);
        float f = func(p, on);

      vec3 col = vec3(0.0);
        col = mix( vec3(0.2,0.1,0.4), vec3(0.3,0.05,0.05), f );
        col = mix( col, vec3(0.9,0.9,0.9), dot(on.zw,on.zw) );
        col = mix( col, vec3(0.4,0.3,0.3), 0.2 + 0.5*on.y*on.y );
        col = mix( col, vec3(0.0,0.2,0.4), 0.5*smoothstep(1.2,1.3,abs(on.z)+abs(on.w)) );
        col = clamp( col*f*2.0, 0.0, 1.0 );
        
    #if 0
        // gpu derivatives - bad quality, but fast
      vec3 nor = normalize( vec3( dFdx(f)*iResolution.x, 6.0, dFdy(f)*iResolution.y ) );
    #else    
        // manual derivatives - better quality, but slower
        vec4 kk;
      vec3 nor = normalize( vec3( func(p+vec2(e,0.0),kk)-f, 
                                    2.0*e,
                                    func(p+vec2(0.0,e),kk)-f ) );
    #endif    

        vec3 lig = normalize( vec3( 0.9, 0.2, -0.4 ) );
        float dif = clamp( 0.3+0.7*dot( nor, lig ), 0.0, 1.0 );
        vec3 lin = vec3(0.70,0.90,0.95)*(nor.y*0.5+0.5) + vec3(0.15,0.10,0.05)*dif;
        col *= 1.2*lin;
        col = 1.0 - col;
        col = 1.1*col*col;
        
        fragColor = vec4( col, 1.0 );
    }

    void main(){
        vec4 a = vec4(1.0, 1.0, 1.0, 1.0);
        vec4 color = vec4(1.0, 1.0, 1.0, 1.0);
        vec4 coord = gl_FragCoord;
        mainImage(color, coord.xy);

        gl_FragColor=color;
    }
    `);
    gl.compileShader(fragShader);


    var glProgram = gl.createProgram();
    gl.attachShader(glProgram, vertShader);
    gl.attachShader(glProgram, fragShader);
    gl.linkProgram(glProgram);
    gl.useProgram(glProgram);

    this.glProgram = glProgram;

    var a_PointSize=gl.getAttribLocation(glProgram,'a_PointSize');
    gl.vertexAttrib1f(a_PointSize,30.0);


    //1.创建缓冲区对象
    var vertexBuffer = gl.createBuffer();
    // 2.绑定缓冲区对象（表明了缓冲区对象的用途）
    gl.bindBuffer(gl.ARRAY_BUFFER, vertexBuffer);
    // 3.向缓冲区对象中写入数据
    var tempData=new Float32Array([
        -1, -1, 
        -1, 1,
        1, 1,
        1, -1,
        -1, -1
    ]);
    gl.bufferData(gl.ARRAY_BUFFER, tempData, gl.STATIC_DRAW);
    // 4.获取变量存储位置
    var a_Position = gl.getAttribLocation(glProgram, 'a_Position');
    // 5.把缓冲区对象分配给a_Position变量
    gl.vertexAttribPointer(a_Position, 2, gl.FLOAT, false, 0, 0);
    // 6.连接缓冲区对象和a_Position变量
    gl.enableVertexAttribArray(a_Position);


    var iTime=gl.getUniformLocation(glProgram,'iTime');
    gl.uniform1f(iTime,3.0);


    gl.drawArrays(gl.TRIANGLE_STRIP, 0, 5);
    //gl.drawArrays(gl.POINTS, 0, 3);
  }

}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
