<script>
    import { onMount } from 'svelte'
    $: innerHeight = 0
    $: innerWidth = 0
    let canvas
    let ctx
    let hue = 0
    let mouse = {
        x: undefined,
        y: undefined,
    }
    let particleArray = []

    onMount(() => {
        ctx = canvas.getContext('2d')
        canvas.width = innerWidth
        canvas.height = innerHeight
        class Particle {
            constructor() {
                this.x = mouse.x
                this.y = mouse.y
                this.size = Math.random() * 1 + 1
                this.speedX = Math.random() * 3 - 1.5
                this.speedY = Math.random() * 3 - 1.5
                this.color = `hsl(${hue}, 100%, 50%)`
            }
            update() {
                this.x += this.speedX
                this.y += this.speedY
                if (this.size > 0.2) this.size -= 0.01
            }
            draw() {
                ctx.fillStyle = this.color
                ctx.beginPath()
                ctx.arc(this.x, this.y, this.size, 0, Math.PI * 2)
                ctx.fill()
            }
        }
        canvas.addEventListener('click', function (event) {
            mouse.x = event.x
            mouse.y = event.y
            for (let i = 0; i < 10; i++) {
                particleArray.push(new Particle())
            }
        })
        canvas.addEventListener('mousemove', function (event) {
            mouse.x = event.x
            mouse.y = event.y
            for (let i = 0; i < 2; i++) {
                particleArray.push(new Particle())
            }
        })
        function handleParticles() {
            for (let i = 0; i < particleArray.length; i++) {
                particleArray[i].draw()
                particleArray[i].update()
                for (let j = i; j < particleArray.length; j++) {
                    let dx = particleArray[i].x - particleArray[j].x
                    let dy = particleArray[i].y - particleArray[j].y
                    let distance = Math.sqrt(dx * dx + dy * dy)
                    if (distance < 100) {
                        ctx.beginPath()
                        ctx.strokeStyle = particleArray[i].color
                        ctx.lineWidth = particleArray[i].size
                        ctx.moveTo(particleArray[i].x, particleArray[i].y)
                        ctx.lineTo(particleArray[j].x, particleArray[j].y)
                        ctx.stroke()
                    }
                }
                if (particleArray[i].size <= 0.3) {
                    particleArray.splice(i, 1)
                    i--
                }
            }
        }

        const animate = () => {
            ctx.fillStyle = 'rgba(164, 233, 234, 0.01)'
            ctx.fillRect(0, 0, canvas.width, canvas.height)
            hue = (hue += 1) % 360
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
