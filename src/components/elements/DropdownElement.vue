<script setup lang="ts">
import { Menu, MenuButton, MenuItem, MenuItems } from '@headlessui/vue';
import { ChevronDownIcon, ChevronUpIcon } from '@heroicons/vue/20/solid';
import { onMounted, ref, type Ref } from 'vue';

const dropdown: Ref<typeof Menu | null> = ref(null);

const optionsOnTop: Ref<boolean> = ref(false);
const optionsOnLeft: Ref<boolean> = ref(false);
const optionsHeight: Ref<number> = ref(200);

function calculateOptionsSizeAndPosition() {
  const topbarHeight = 50;
  const minPossileHeight = 32;
  const maxPossileHeight = 192;

  if (!dropdown.value || !dropdown.value.$el) {
    return;
  }
  const el = dropdown.value.$el as HTMLDivElement;
  const rect = el.getBoundingClientRect();

  const spaceAbove = rect.top - topbarHeight;
  const spaceBelow = window.innerHeight - rect.bottom;
  const highestSpace = Math.max(spaceAbove, spaceBelow);

  optionsOnTop.value = spaceBelow >= maxPossileHeight ? false : highestSpace === spaceAbove;
  optionsHeight.value = Math.min(
    Math.max(Math.min(highestSpace - minPossileHeight, maxPossileHeight), minPossileHeight),
    maxPossileHeight
  );

  const spaceLeft = rect.left;
  const spaceRight = window.innerWidth - rect.right;

  const highestSpaceLeftOrRight = Math.max(spaceLeft, spaceRight);

  optionsOnLeft.value = spaceLeft >= highestSpaceLeftOrRight;
}

onMounted(() => {
  calculateOptionsSizeAndPosition();
  window.addEventListener('resize', calculateOptionsSizeAndPosition);
});
</script>

<template>
  <Menu ref="dropdown" as="div" class="w-fit relative" v-slot="{ open }">
    <MenuButton as="div">
      <slot>
        <button type="button" class="w-full p-2 rounded dark:bg-black-lighter bcustom-ring">
          <div class="flex items-center justify-between gap-1">
            <span>Options</span>
            <span class="w-5 h-5">
              <ChevronDownIcon
                v-if="!optionsOnTop"
                class="h-5 w-5 transition"
                :class="{ 'transform -rotate-180': open }"
                aria-hidden="true"
              />
              <ChevronUpIcon
                v-if="optionsOnTop"
                class="h-5 w-5 transition"
                :class="{ 'transform rotate-180': open }"
                aria-hidden="true"
              />
            </span>
          </div>
        </button>
      </slot>
    </MenuButton>

    <MenuItems
      as="ul"
      class="overflow-auto z-50 absolute p-1 rounded bg-white dark:bg-black-lighter shadow bcustom-ring"
      :class="[
        optionsOnTop ? 'bottom-full mb-1' : 'top-full mt-1',
        optionsOnLeft ? 'right-0' : 'left-0'
      ]"
    >
      <MenuItem as="template" v-slot="{ active }">
        <li
          class="w-full flex items-center gap-2 p-2 rounded cursor-pointer"
          :class="{ 'text-white bg-primary': active }"
        >
          <span class="truncate">Editfs</span>
        </li>
      </MenuItem>
    </MenuItems>
  </Menu>
</template>
