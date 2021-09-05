<script>
import { h } from 'vue'
export default {
    name: "UserEmail",
    render() {
        return h('a', {
            class: 'linkclass',
            href: 'mailto:' + this.email
        }, this.email)
    },
    props: {
        email: String
    }
    
}
</script>
