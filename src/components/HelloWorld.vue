<script lang="ts">
import { defineComponent, onMounted, ref, watch } from "vue";

const caret = `<svg viewBox="0 0 14 14" fill="none" xmlns="http://www.w3.org/2000/svg">
<rect x="7" y="0.635742" width="9" height="1" transform="rotate(45 7 0.635742)" fill="black"/>
<rect x="13.3633" y="7" width="9" height="1" transform="rotate(135 13.3633 7)" fill="black"/>
</svg>`;

export default defineComponent({
  props: {
    onSelect: {
      type: (Function as unknown) as () => (start: Date, end: Date) => void,
      required: true,
    },
  },

  setup(props) {
    const inputRef = ref<null | HTMLInputElement>(null);
    const isReady = ref(false);
    let picker;
    onMounted(async () => {
      (window as any).disableLitepickerStyles = true;

      // Initialize
      const { default: Litepicker } = await import("litepicker");
      picker = new Litepicker({
        element: inputRef.value,
        numberOfMonths: 2,
        numberOfColumns: 1,
        mobileFriendly: true,
        maxDays: null,
        singleMode: false,
        showTooltip: false,
        buttonText: {
          previousMonth: caret,
          nextMonth: caret,
        },
        onSelect(start: Date, end: Date) {
          props.onSelect(start, end);
        },
        onRenderDay(element: HTMLElement) {
          element.innerHTML = `<span>${element.innerHTML}</span>`;
        },
        onRender(element: HTMLElement) {
          element
            .querySelectorAll(".month-item-weekdays-row")
            .forEach((weekRow) => {
              Array.from(weekRow.children).forEach((dayNumberEl) => {
                const day = dayNumberEl.getAttribute("title")?.toLowerCase();
                // dayNumberEl.innerHTML = $i18n
                //   .t(`general.datepicker.${day}`)
                //   .toString();
                dayNumberEl.innerHTML = day?.slice(0, 1).toUpperCase() || "";
              });
            });
        },
        inlineMode: true,
      });

      // Inject

      // Set ready as true (for potential disable toggle)
      isReady.value = true;
    });

    const show = () => picker.show();
    return { inputRef, show, isReady };
  },
});
</script>

<template>
  <div class="datepicker">
    <div class="trigger" @click="show">
      <slot name="trigger" :show="show" :isReady="isReady">
        <button>Select</button>
      </slot>
    </div>
    <input ref="inputRef" class="litepicker-input-element" />
    <!-- IF INLINE, Litepicker renders right here -->
  </div>
</template>

<style lang="scss">
// NOT SCOPED
// @import "~/styles/core/variables.scss";

$litepickerDayWidth: 36px;

$litepickerBgColor: #fff;
$litepickerMonthHeaderTextColor: #333;
$litepickerMonthButton: #9e9e9e;
$litepickerMonthButtonHover: #37b4fc;
$litepickerMonthWidth: $litepickerDayWidth * 7; // 7 days
$litepickerMonthWeekdayColor: #9e9e9e;

$litepickerDayColor: #333;
$litepickerDayColorHover: #37b4fc;
$litepickerDayIsTodayColor: #f44336;
$litepickerDayIsInRange: #ebf7ff;
$litepickerDayIsLockedColor: #9e9e9e;
$litepickerDayIsBookedColor: #9e9e9e;
$litepickerDayIsStartColor: #fff;
$litepickerDayIsStartBg: #37b4fc;
$litepickerDayIsEndColor: #fff;
$litepickerDayIsEndBg: #37b4fc;

$litepickerButtonCancelColor: #fff;
$litepickerButtonCancelBg: #9e9e9e;
$litepickerButtonApplyColor: #fff;
$litepickerButtonApplyBg: #37b4fc;

$litepickerButtonResetBtn: #909090;
$litepickerButtonResetBtnHover: #37b4fc;

$litepickerHighlightedDayColor: #333;
$litepickerHighlightedDayBg: #ffeb3b;

.litepicker {
  font-size: 14px;
  display: none;

  .container {
    &__main {
      display: flex;
    }
    &__months {
      display: flex;
      flex-wrap: wrap;
      background-color: $litepickerBgColor;
      border-radius: 5px;
      width: calc(
        #{$litepickerMonthWidth} + 10px
      ); // 10px is padding (left 5px, right: 5px)
      box-sizing: content-box;

      &.columns-2 {
        width: calc((#{$litepickerMonthWidth * 2}) + 20px);
      }

      &.columns-3 {
        width: calc((#{$litepickerMonthWidth * 3}) + 30px);
      }

      &.columns-4 {
        width: calc((#{$litepickerMonthWidth * 4}) + 40px);
      }

      &.split-view {
        .month-item {
          &-header {
            .button-previous-month,
            .button-next-month {
              visibility: visible;
            }
          }
        }
      }

      .month-item {
        padding: 5px;
        width: $litepickerMonthWidth;
        box-sizing: content-box;

        &-header {
          display: flex;
          justify-content: space-between;
          font-weight: 500;
          padding: 10px 5px;
          text-align: center;
          align-items: center;
          color: $litepickerMonthHeaderTextColor;

          div {
            flex: 1;

            > .month-item-name {
              margin-right: 5px;
            }

            > .month-item-year {
              padding: 0;
              font-weight: 600;
            }
          }

          .reset-button {
            color: $litepickerButtonResetBtn;

            > svg,
            > img {
              fill: $litepickerButtonResetBtn;
              pointer-events: none;
            }

            &:hover {
              color: $litepickerButtonResetBtnHover;

              > svg {
                fill: $litepickerButtonResetBtnHover;
              }
            }
          }

          .button-previous-month,
          .button-next-month {
            visibility: hidden;
            text-decoration: none;
            color: $litepickerMonthButton;
            padding: 3px 5px;
            border-radius: 3px;
            transition: color 0.3s, border 0.3s;
            cursor: default;

            > svg,
            > img {
              fill: $litepickerMonthButton;
              pointer-events: none;
              width: 14px;
              height: 14px;
            }

            &:hover {
              color: $litepickerMonthButtonHover;

              > svg {
                fill: $litepickerMonthButtonHover;
              }
            }
          }
        }

        .button-previous-month {
          svg {
            transform: scaleX(-1);
          }
        }

        &-weekdays-row {
          display: flex;
          justify-self: center;
          justify-content: flex-start;
          color: $litepickerMonthWeekdayColor;

          > div {
            padding: 5px 0;
            font-size: 85%;
            flex: 1;
            width: $litepickerDayWidth;
            text-align: center;
          }
        }

        &:first-child {
          .button-previous-month {
            visibility: visible;
          }
        }

        &:last-child {
          .button-next-month {
            visibility: visible;
          }
        }

        &.no-previous-month {
          .button-previous-month {
            visibility: hidden;
          }
        }

        &.no-next-month {
          .button-next-month {
            visibility: hidden;
          }
        }
      }
    }

    &__days {
      display: flex;
      flex-wrap: wrap;
      justify-self: center;
      justify-content: flex-start;
      text-align: center;
      box-sizing: content-box;

      > div,
      > a {
        padding: 7px 0;
        width: $litepickerDayWidth;
      }

      .day-item {
        color: $litepickerDayColor;
        text-align: center;
        text-decoration: none;
        border-radius: 3px;
        cursor: default;
        margin-top: 8px;

        span {
          pointer-events: none;
        }

        &.is-today {
          color: $litepickerDayIsTodayColor;
        }

        &.is-locked {
          color: $litepickerDayIsLockedColor;

          &:hover {
            color: $litepickerDayIsLockedColor;
            box-shadow: none;
            cursor: default;
          }
        }

        &.is-in-range {
          background-color: $litepickerDayIsInRange;
          border-radius: 0;
          position: relative;
          z-index: 1;
          color: #37b4fc;
          font-weight: 600;
        }

        &:hover {
          position: relative;

          &::before {
            content: "";
            display: block;
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            z-index: 1;
            background-color: transparentize($litepickerDayIsEndBg, 0.5);
            border-radius: 50%;
          }
        }

        &.is-start-date,
        &.is-end-date {
          color: $litepickerDayIsEndColor;
          display: inline-block;
          position: relative;
          font-weight: 600;

          &::before {
            content: "";
            display: block;
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            z-index: 1;
            background-color: $litepickerDayIsEndBg;
            border-radius: 50%;
          }

          &::after {
            content: "";
            background: $litepickerDayIsInRange;
            position: absolute;
            top: 0;
            width: 50%;
            height: 100%;
            left: 50%;
          }

          span {
            position: relative;
            z-index: 2;
          }
        }

        &.is-end-date:not(.is-flipped),
        &.is-start-date.is-flipped {
          &::after {
            content: "";
            background: $litepickerDayIsInRange;
            position: absolute;
            top: 0;
            width: 50%;
            height: 100%;
            left: -25%;
          }
        }

        &.is-end-date:first-of-type {
          &::after {
            display: none;
          }
        }

        &.is-start-date:not(.is-flipped):last-child {
          &::after {
            display: none;
          }
        }

        &.is-start-date.is-end-date {
          &::after {
            display: none;
          }
        }

        // Fade effect on beginning and end of month if range spans over

        &.is-in-range:last-child,
        &.is-in-range:first-of-type {
          &::after {
            content: "";
            width: 100%;
            height: 100%;
            position: absolute;
            left: 100%;
            background: linear-gradient(
              90deg,
              $litepickerDayIsInRange 0%,
              rgba(55, 180, 252, 0) 100%
            );
            top: 0;
          }
        }

        &.is-in-range:first-of-type {
          &::after {
            left: -100%;
            transform: scaleX(-1);
          }
        }

        &.is-highlighted {
          color: $litepickerHighlightedDayColor;
          background-color: $litepickerHighlightedDayBg;
        }
      }

      .week-number {
        display: flex;
        align-items: center;
        justify-content: center;
        color: #9e9e9e;
        font-size: 85%;
      }
    }

    &__footer {
      text-align: right;
      padding: 10px 5px;
      margin: 0 5px;
      background-color: #fafafa;
      box-shadow: inset 0px 3px 3px 0px #ddd;
      border-bottom-left-radius: 5px;
      border-bottom-right-radius: 5px;

      .preview-date-range {
        margin-right: 10px;
        font-size: 90%;
      }

      .button-cancel {
        background-color: $litepickerButtonCancelBg;
        color: $litepickerButtonCancelColor;
        border: 0;
        padding: 3px 7px 4px;

        > svg,
        > img {
          pointer-events: none;
        }
      }

      .button-apply {
        background-color: $litepickerButtonApplyBg;
        color: $litepickerButtonApplyColor;
        border: 0;
        padding: 3px 7px 4px;
        margin-left: 10px;
        margin-right: 10px;

        &:disabled {
          opacity: 0.7;
        }

        > svg,
        > img {
          pointer-events: none;
        }
      }
    }
  }

  &-open {
    overflow: hidden;
  }

  &-backdrop {
    display: none;
    background-color: #000;
    opacity: 0.3;
    position: fixed;
    top: 0;
    right: 0;
    bottom: 0;
    left: 0;
  }
}

// Adjustments

// Hide week days on subsequent months
.month-item:not(:first-child) {
  .month-item-weekdays-row {
    display: none;
  }
}

.litepicker-input-element {
  visibility: hidden;
}
</style>
