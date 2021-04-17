<script>
    import { onMount } from 'svelte'
    $: innerHeight = 0
    $: innerWidth = 0
    let canvas
    let ctx

    let particleArray = []

    onMount(() => {
        ctx = canvas.getContext('2d')
        canvas.width = innerWidth
        canvas.height = innerHeight
        class Particle {
            constructor() {
                this.x = Math.random() * canvas.width
                this.y = Math.random() * canvas.height
                this.size = Math.random() * 5 + 1
                this.speedX = Math.random() * 1 - 1.5
                this.speedY = Math.random() * 3 - 1.5
            }
            update() {
                this.x += this.speedX
                this.y += this.speedY
            }
            draw() {
                ctx.fillStyle = 'pink'
                ctx.beginPath()
                ctx.arc(this.x, this.y, 50, 0, Math.PI * 2)
                ctx.fill()
            }
        }

        function init() {
            for (let i = 0; i < 100; i++) {
                particleArray.push(new Particle())
            }
            console.log(particleArray)
        }
        init()
        function handleParticles() {
            for (let i = 0; i < particleArray.length; i++) {
                particleArray[i].draw()
                particleArray[i].update()
            }
        }

        const animate = () => {
            ctx.clearRect(0, 0, canvas.width, canvas.height)
            handleParticles()
            requestAnimationFrame(animate)
        }
        animate()
    })
</script>

<svelte:window bind:innerWidth bind:innerHeight />

<div
    class="container"
    bind:clientWidth={innerWidth}
    bind:clientHeight={innerHeight}
>
    <canvas bind:this={canvas} />
</div>

<style>
    .container {
        width: 100%;
        height: 100%;
    }
</style>
