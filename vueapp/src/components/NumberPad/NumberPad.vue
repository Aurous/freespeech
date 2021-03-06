<template>
  <v-card class="numberPad white">
    <v-card-title
      v-if="title"
      class="title grey lighten-2"
    >
      {{ title }}
    </v-card-title>

    <v-card-text class="pt-4">
      <v-row>
        <v-col cols="12">
          <v-card>
            <v-card-text class="text-center title">
              &nbsp;{{ display }}&nbsp;
            </v-card-text>
          </v-card>
        </v-col>
      </v-row>

      <v-row class="d-flex">
        <template v-for="n in 9">
          <v-col
            cols="4"
            :key="n"
          >
            <v-btn
              block
              @click="padInput(n)"
              x-large
              :disabled="!isNumbersEnabled"
            >
              {{ n }}
            </v-btn>
          </v-col>
        </template>

        <v-col cols="4">
          <v-btn
            block
            @click="backspace"
            x-large
            color="warning"
            v-if="isBackspaceEnabled"
          >
            <v-icon>backspace</v-icon>
          </v-btn>

          <v-btn
            block
            @click="close"
            x-large
            color="error"
            v-else
          >
            <v-icon>close</v-icon>
          </v-btn>
        </v-col>

        <v-col cols="4">
          <v-btn
            block
            @click="padInput(0)"
            x-large
            :disabled="!isNumbersEnabled"
          >
            0
          </v-btn>
        </v-col>

        <v-col cols="4">
          <v-btn
            block
            @click="save"
            x-large
            color="success"
            :disabled="!isSaveEnabled"
          >
            <v-icon>done</v-icon>
          </v-btn>
        </v-col>
      </v-row>
    </v-card-text>
  </v-card>
</template>

<script>
export default {
	name: 'NumberPad',
	props: {
		hidden: {
			type: Boolean,
			default: false
		},
		length: {
			type: Number,
			default: 0
		},
		title: {
			type: String,
			default: null
		}
	},
	data() {
		return {
			input: '',
			hiddenCharacter: '⚫'
		};
	},
	computed: {
		display() {
			if (this.hidden) {
				return this.hiddenCharacter.repeat(this.input.length);
			}

			return this.input;
		},
		isBackspaceEnabled() {
			return this.input.length > 0;
		},
		isNumbersEnabled() {
			if (this.length) {
				return this.input.length < this.length;
			} else {
				return true;
			}
		},
		isSaveEnabled() {
			if (this.length) {
				return this.input.length === this.length;
			} else {
				return this.input.length > 0;
			}
		}
	},
	methods: {
		backspace() {
			if (this.isBackspaceEnabled) {
				this.input = this.input.substring(0, this.input.length - 1);
			}
		},
		close() {
			if (!this.isBackspaceEnabled) {
				this.input = '';
				this.$emit('input', null);
			}
		},
		keyboardInput(e) {
			if (e.key >= 0 && e.key <= 9) {
				e.preventDefault();
				this.padInput(e.key);
			} else if (e.key === 'Enter') {
				e.preventDefault();
				this.save();
			} else if (e.key === 'Escape') {
				e.preventDefault();
				this.close();
			} else if (e.key === 'Backspace') {
				e.preventDefault();
				this.backspace();
			}
		},
		padInput(number) {
			if (this.isNumbersEnabled) {
				this.input = this.input + number;
			}
		},
		save() {
			if (this.isSaveEnabled) {
				this.$emit('input', this.input);
				this.input = '';
			}
		}
	},
	created: function() {
		window.addEventListener('keyup', this.keyboardInput);
	},
	beforeDestroy: function() {
		window.removeEventListener('keyup', this.keyboardInput);
	}
};
</script>

<style scoped>

</style>
