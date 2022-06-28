<script>
import { ref, h, Transition, computed } from "vue";

export default {
  props: {
    direction: { type: String, default: "vertical" },
  },
  setup(props, { slots }) {
    let touchStartTime, touchEndTime;
    let clientY, clientX;

    const defaultSlots = slots.default().length === 1 ? slots.default()[0].children : slots.default();

    const current = ref(1);
    const anm = ref("-up");

    const transistionName = computed(() => props.direction + anm.value);
    const isRow = computed(() => (props.direction === "horizontal" ? true : false));
    const isVertical = computed(() => (props.direction == "vertical" ? true : false));

    function touchstart(e) {
      e.preventDefault();
      touchStartTime = Date.now();
      clientY = e.touches[0].clientY;
      clientX = e.touches[0].clientX;
    }
    function touchend(e) {
      touchEndTime = Date.now();
      slide(e);
    }
    function next() {
      current.value = (current.value % defaultSlots.length) + 1;
    }
    function previous() {
      current.value - 1 == 0 ? (current.value = defaultSlots.length) : (current.value = current.value - 1);
    }
    function slideUp() {
      anm.value = "-up";
      next();
    }
    function slideDown() {
      anm.value = "-down";
      previous();
    }
    function slideRight() {
      previous();
      anm.value = "-right";
    }
    function slideLeft() {
      next();
      anm.value = "-left";
    }
    function slide(e) {
      if (touchEndTime - touchStartTime <= 500) {
        if (clientY - e.changedTouches[0].clientY >= 100 && isVertical.value) slideUp();
        else if (clientY - e.changedTouches[0].clientY <= -100 && isVertical.value) slideDown();
        else if (clientX - e.changedTouches[0].clientX <= -100) slideRight();
        else if (clientX - e.changedTouches[0].clientX >= 100) slideLeft();
      }
    }

    return () =>
      h(
        "div",
        { class: "carousel" },
        defaultSlots.map((item, index) => {
          if (current.value == index + 1) {
            return h(
              Transition,
              {
                name: transistionName.value,
                ontouchstart: touchstart,
                ontouchend: touchend,
                class: { carousel__item: true, row: isRow.value },
              },
              () => item
            );
          }
        })
      );
  },
};
</script>

<style>
.column {
  flex-direction: column;
}
.row {
  flex-direction: row;
}
.carousel {
  width: 300px;
  height: 600px;
  max-height: 100vh;
  overflow: hidden;
  display: flex;
  position: relative;
}
.carousel > * {
  width: 100%;
  height: 100%;
  flex-shrink: 0;
  position: absolute;
  top: 0;
  left: 0;
}

.vertical-up-enter-active,
.vertical-up-leave-active,
.vertical-down-leave-active,
.vertical-down-enter-active,
.horizontal-left-enter-active,
.horizontal-left-leave-active,
.horizontal-right-leave-active,
.horizontal-right-enter-active {
  transition: all 2s ease-in-out;
}

.vertical-up-enter-from {
  transform: translateY(100%);
}

.vertical-up-enter-to {
  transform: translateY(0);
}

.vertical-up-leave-to {
  transform: translateY(-100%);
}

.vertical-up-leave-from {
  transform: translateY(0);
}

.vertical-down-enter-from {
  transform: translateY(-100%);
}
.vertical-down-enter-to {
  transform: translateY(0);
}

.vertical-down-leave-to {
  transform: translateY(100%);
}

.vertical-down-leave-from {
  transform: translateY(0);
}

.horizontal-right-enter-from {
  transform: translateX(-100%);
}
.horizontal-right-enter-to {
  transform: translateX(0);
}

.horizontal-right-leave-from {
  transform: translateX(0);
}
.horizontal-right-leave-to {
  transform: translateX(100%);
}

.horizontal-left-enter-to {
  transform: translate(0);
}

.horizontal-left-enter-from {
  transform: translate(100%);
}

.horizontal-left-leave-from {
  transform: translate(0);
}

.horizontal-left-leave-to {
  transform: translate(-100%);
}
</style>
