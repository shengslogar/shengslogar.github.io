<template>
    <div class="landing-business-card-content-wrapper">
        <div class="landing-business-card-transform-wrapper"
             :style="{ transform }">
            <div class="landing-business-card">
                <div class="landing-business-card__embellishment-list">
                    <div class="landing-business-card__embellishment-1"></div>
                    <div class="landing-business-card__embellishment-2"></div>
                    <div class="landing-business-card__embellishment-3"></div>
                </div>
                <div class="landing-business-card__info">
                    <h2 class="landing-business-card__name">
                        Sheng<br>
                        Slogar
                    </h2>
                    <div class="landing-business-card__details">
                        SLC, UT, USA | sheng@diglactic.com
                    </div>
                </div>
                <div class="landing-business-card__occupation">
                    Web Developer
                </div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    name: 'LandingBusinessCard',
    data() {
        return {
            motionTransform: {x: 0, y: 0},
            isMobile: false
        };
    },
    computed: {
        transform() {
            const {x, y} = this.motionTransform;
            const z = this.isMobile
                ? 90
                : 0;

            return `rotateX(${Math.round(x)}deg) rotateY(${Math.round(y)}deg) rotateZ(${z}deg)`;
        }
    },
    methods: {
        setupResponsiveLogic() {
            const updateLayout = () => {
                this.isMobile = window.outerWidth <= 525;
            };

            window.addEventListener('resize', () => {
                updateLayout();
            });

            updateLayout();
        },
        setupMouseLogic() {
            const container = document.body;

            window.addEventListener('mousemove', ({pageX, pageY}) => {
                // get center of element in pixels
                const centerX = container.scrollWidth / 2;
                const centerY = container.scrollHeight / 2;

                // treat (0, 0) as center of element
                const relX = (pageX - centerX) / centerX;
                const relY = (pageY - centerY) / centerY;

                this.motionTransform = {
                    x: -relY * 10,
                    y: relX * 10
                };
            });
        },
        setupMotionLogic() {
            // gather orientation at page load
            let initialOrientation = null;

            window.addEventListener('deviceorientation', ({alpha, beta}) => {
                if (initialOrientation !== null) {
                    const movement = {
                        x: beta - initialOrientation.beta,
                        y: alpha - initialOrientation.alpha
                    };

                    this.motionTransform = {
                        x: -movement.x / 10,
                        y: movement.y / 10
                    };
                } else {
                    initialOrientation = {alpha, beta};
                }
            });
        }
    },
    mounted() {
        this.setupResponsiveLogic();
        this.setupMotionLogic();

        // don't setup mouse logic on touch devices
        if (!('ontouchstart' in window)) {
            this.setupMouseLogic();
        }
    }
};
</script>

<style lang="scss">
.landing-business-card {
    $padding: 1.5rem;
    $width: 525px;
    $height: 300px;

    width: $width;
    height: $height;
    background: #fff;
    position: relative;
    border-radius: .1rem;
    backface-visibility: hidden;
    animation: landing-business-card--entrance .45s ease;

    @keyframes landing-business-card--entrance {
        from {
            opacity: 0;
            transform: translateY(1rem);
        }
    }

    &__embellishment {
        &-list {
            position: absolute;
            top: 1.5rem;
            right: 0;
            flex-direction: column;
            display: flex;
            align-items: flex-end;
        }

        &-1,
        &-2,
        &-3 {
            height: .5rem;
            background: #202328;
            margin: .5rem 0;
        }

        &-1 {
            width: 10rem;
        }

        &-2 {
            width: 8rem;
        }

        &-3 {
            width: 12rem;
        }
    }

    &__info {
        position: absolute;
        bottom: $padding;
        left: $padding;
    }

    &__name {
        font-size: 3rem;
    }

    &__details {
        text-transform: lowercase;
        font-size: .75rem;
        margin: 1rem 0 0;
    }

    &__occupation {
        font-size: .85rem;
        text-transform: uppercase;
        transform: rotateZ(-90deg);
        transform-origin: 100% 100%;
        position: absolute;
        bottom: 10rem + $padding;
        right: $padding;
        width: 10rem;
        color: #999;
    }

    &-content-wrapper {
        perspective: 1000px;
    }

    &-transform-wrapper {
        transition: transform .1s linear;
    }

    @media (max-width: $width) {
        animation: none;

        &-content-wrapper {
            margin: 6rem 0;
            width: $height;
            height: $width;
        }
    }
}
</style>
