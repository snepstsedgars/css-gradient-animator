<template>
  <div>
    <Notification v-if=notification.show />
    <Sidebar :hideSidebar="hideSidebar" />
    <div class="intro" :class="{ 'fadeOut': codeBox, 'fadeIn': !codeBox }">
      <p>CSS Gradient Animator</p>
      <button @click.stop="toggleCodeBox">View Code <Code/></button>
      <p class="unsupported-screen-size">The tool is designed for the desktop.</p>
    </div>
    <p class="instruction">
      hit <strong>spacebar</strong> to randomize or hit <strong>c</strong> to copy
      the code
    </p>
    <code :class="{ 'fadeIn': codeBox, 'fadeOut': !codeBox }" v-click-outside="closeCodeBox">
      <a href="#" @click.prevent="closeCodeBox" class="close-codebox"><Remove/></a>
      <pre>
        {{ this.configureGradient().replace(/^ {8}/gm, '') }}
      </pre>
      <a href="#" class="copy-code" @click.stop="copyCode">Click here to Copy code</a>
    </code>
  </div>
</template>

<script>
import { mapActions, mapState } from 'vuex';
import Config from '@/config';
import Remove from '@/assets/close.svg';
import Code from '@/assets/code.svg';
import Sidebar from '@/components/Sidebar';
import Generator from '@/mixins/Generator';
import Notification from '@/components/Notification';

export default {
  name: 'Home',
  metaInfo: {
    title: 'CSS Gradient Animator',
    titleTemplate: '%s',
    htmlAttrs: {
      lang: 'en',
      amp: true,
    },
  },
  components: {
    Sidebar,
    Notification,
    Code,
    Remove,
  },
  mixins: [Generator],
  data() {
    return {
      hideSidebar: false,
      codeBox: false,
    };
  },
  computed: {
    ...mapState('Notification', ['notification']),
  },
  mounted() {
    const externalScript = document.createElement('script');
    externalScript.setAttribute('src', 'https://buttons.github.io/buttons.js');
    externalScript.setAttribute('defer', 'defer');
    document.head.appendChild(externalScript);

    window.addEventListener('keyup', (e) => {
      switch (e.keyCode) {
        case 32:
          this.applyPresets(Config.presets[Math.floor(Math.random() * Config.presets.length)]);
          this.showNotification({
            show: true,
            message: 'Random Gradient Palettes Applied',
          });
          this.setGradient();
          break;
        case 86:
          this.toggleCodeBox();
          break;
        case 67:
          this.copyCode();
          break;
        case 72:
          this.toggleSidebar();
          break;
        default:
          break;
      }
    });
  },
  methods: {
    ...mapActions('Palette', ['applyPresets', 'toggleCodeBox']),
    ...mapActions('Notification', ['showNotification']),
    toggleCodeBox() {
      this.codeBox = !this.codeBox;
    },
    closeCodeBox() {
      this.codeBox = false;
    },
    toggleSidebar() {
      this.hideSidebar = !this.hideSidebar;
    },
    async copyCode() {
      await this.$copyText(this.configureGradient());
      this.showNotification({
        show: true,
        message: 'Code Copied',
      });
    },
  },
};
</script>

<style lang="scss" scoped>
pre {
  font-family: Ubuntu Mono;
}

code {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  line-height: 24px;
  padding: 20px 40px;
  border: 1px solid var(--white);
  visibility: hidden;
  border-radius: 3px;
}

button,
.mobile-github-button {
  background-color: transparent;
  border: 1px solid var(--white);
  padding: 10px 20px;
  border-radius: 3px;
  cursor: pointer;
  font-family: inherit;
  font-size: inherit;
  color: var(--white);
  margin-top: 40px;
  text-transform: uppercase;
  word-spacing: 2px;
  font-size: 14px;
  font-weight: bold;
  letter-spacing: 1px;
  transition: background-color 250ms ease;
  line-height: 21px;
  position: relative;
  padding-left: 50px;

  &:hover {
    background-color: var(--white-10);
  }

  svg {
    position: absolute;
    width: 24px;
    left: 10%;
    top: 50%;
    transform: translateY(-50%);
    margin-top: -1px; //todo because am getting too old to handle css these days
    fill: var(--white);
  }
}

.mobile-github-button {
  padding-left: 60px;
  display: none;
}

.copy-code {
  position: absolute;
  bottom: 0;
  width: 100%;
  background-color: var(--white);
  left: 0;
  bottom: 0;
  color: var(--black-80);
  text-align: center;
  padding: 5px;
}

.close-codebox {
  position: absolute;
  top: -20px;
  right: -20px;

  svg {
    width: 40px;
    background-color: var(--black-80);
    border-radius: 50%;
  }
}

.intro {
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  position: absolute;
  text-transform: uppercase;
  text-align: center;

  h1 {
    font-family: Lombok;
    font-size: 52px;
    margin-bottom: 20px;
  }

  p {
    font-size: 24px;
    letter-spacing: 1px;

    &.unsupported-screen-size {
      display: none;
      margin-top: 40px;
      font-size: 16px;
    }
  }
}

.unsupported-screen-size {
  svg {
    display: block;
    width: 50px;
    height: 50px;
    margin: auto;
    margin-top: 40px;
  }
}

.instruction {
  position: absolute;
  bottom: 40px;
  transform: translateX(-50%);
  left: 50%;
}

@media all and (max-width: 768px) {
  button,
  .instruction {
    display: none;
  }

  .intro {
    white-space: nowrap;
  }

  .intro {
    p {
      &.unsupported-screen-size {
        display: block;
      }
    }
  }

  .mobile-github-button {
    display: inline-block;
  }
}
</style>
