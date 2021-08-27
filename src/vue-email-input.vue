<template>
    <v-select :taggable="true" :multiple="true" v-model="emails" :name="name"
              :clear-search-on-blur="clearSearchOnBlur" :disabled="disabled" :no-drop="true"
              :placeholder="placeholder" @search="selectSearch" ref="vSelect">
        <div slot="no-options">{{ placeholder }}</div>
    </v-select>
</template>

<script>
import vSelect from "vue-select";

export default {
    name: "vue-email-input",
    props: {
        placeholder: {
            type: String,
            default: ""
        },
        name: {
            type: String,
            default: ""
        },
        disabled: {
            type: Boolean,
            default: false
        },
        value: {
            type: Array,
            default: () => []
        }
    },
    data() {
        return {
            emails: [],
            search: ""
        }
    },
    components: {
        vSelect
    },
    created() {
        this.emails = JSON.parse(JSON.stringify(this.value))
    },
    watch: {
        value: {
            deep: true,
            handler() {
                this.emails = JSON.parse(JSON.stringify(this.value))
            }
        },
        emails: {
            deep: true,
            handler() {
                if (this.value.length != this.emails.length) {
                    this.$emit('input', this.emails);
                }
            }
        },
    },
    methods: {
        clearSearchOnBlur() {
            if (!this.emails.includes(this.search)) {
                this.emails.push(this.search);
                this.removeNonEmails()
            }
            return true;
        },
        removeNonEmails() {
            this.emails = this.emails.filter(email => {
                return /^\w+([\.-]?\w+)*@\w+([\.-]?\w+)*(\.\w{2,3})+$/.test(email)
            })
        },
        selectSearch(search) {
            this.search = search;
            if (/[\s,;]+/.test(search)) {
                const emails = search.split(/[\s,;]+/);
                if (emails.length > 0) {
                    emails.forEach(email => {
                        if (!this.emails.includes(email))
                            this.emails.push(email)
                    })
                    this.removeNonEmails()
                    this.$refs.vSelect.search = '';
                    this.$refs.vSelect.searchEl.focus();
                }
            }
            if (search === "")
                this.removeNonEmails()
        }
    }
}
</script>
