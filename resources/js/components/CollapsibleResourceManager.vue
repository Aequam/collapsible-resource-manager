<template>

    <div v-if="!isEmpty || data.linkTo" :class="[ data.type, { 'mb-6': isTopLevel }, 'select-none' ]">

        <component v-if="data.label && isTopLevel" v-bind="topLevelLink"
                   @click="toggleTopLevel"
                   :class="{ 'cursor-pointer': isTopCollapsible }"
                   class="flex flex-1 items-center font-normal text-90 text-base no-underline relative">

            <div v-if="data.icon" class="sidebar-icon" v-html="data.icon"/>

            <svg v-else class="sidebar-icon" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20">
                <path fill="var(--sidebar-icon)"
                      d="M3 1h4c1.1045695 0 2 .8954305 2 2v4c0 1.1045695-.8954305 2-2 2H3c-1.1045695 0-2-.8954305-2-2V3c0-1.1045695.8954305-2 2-2zm0 2v4h4V3H3zm10-2h4c1.1045695 0 2 .8954305 2 2v4c0 1.1045695-.8954305 2-2 2h-4c-1.1045695 0-2-.8954305-2-2V3c0-1.1045695.8954305-2 2-2zm0 2v4h4V3h-4zM3 11h4c1.1045695 0 2 .8954305 2 2v4c0 1.1045695-.8954305 2-2 2H3c-1.1045695 0-2-.8954305-2-2v-4c0-1.1045695.8954305-2 2-2zm0 2v4h4v-4H3zm10-2h4c1.1045695 0 2 .8954305 2 2v4c0 1.1045695-.8954305 2-2 2h-4c-1.1045695 0-2-.8954305-2-2v-4c0-1.1045695.8954305-2 2-2zm0 2v4h4v-4h-4z"/>
            </svg>

            <Badge :label="data.badge" :dim="isTopCollapsible || data.linkTo">

                <span class="flex text-90 sidebar-label">
                    {{ data.label }}
                </span>

                <CollapsibleIndicator :expanded="topExpanded" :visible="isTopCollapsible"/>

            </Badge>

        </component>

        <CollapseTransition :duration="150">

            <ResourceList class="resources-only"
                          v-if="isTopLevel && data.resources.length && (!isTopCollapsible || topExpanded)"
                          :resources="data.resources"
                          :recursive="recursive"
                          :remember-menu-state="rememberMenuState"/>

        </CollapseTransition>

        <template v-if="isGroup && data.resources.length">

            <h4 class="relative select-none ml-8 mt-4 text-xs text-90 uppercase tracking-wide cursor-pointer"
                v-if="data.label"
                @click="toggleGroup(data.id)">

                <CollapsibleIndicator :expanded="activeMenu[data.id]" :visible="isTopCollapsible"/>

                <Badge :label="data.badge">
                    {{ data.label }}
                </Badge>

            </h4>

            <CollapseTransition :duration="150">

                <ResourceList v-if="activeMenu[data.id]"
                              :resources="data.resources"
                              :recursive="recursive"
                              :remember-menu-state="rememberMenuState"/>

            </CollapseTransition>

        </template>

    </div>

</template>

<script>

    import { CollapseTransition } from 'vue2-transitions'
    import ResourceList from './ResourceList'
    import Badge from './Badge'
    import CollapsibleIndicator from './CollapsibleIndicator'

    export default {
        name: 'CollapsibleResourceManager',
        components: { CollapsibleIndicator, CollapseTransition, ResourceList, Badge },
        props: {
            data: { type: Object, required: true },
            rememberMenuState: { type: Boolean, default: false },
            recursive: { type: Boolean, default: false }
        },
        data() {
            return {
                topExpanded: this.data.expanded,
                activeMenu: { [ this.data.id ]: this.data.expanded }
            }
        },
        created() {

            if (this.rememberMenuState) {

                const state = localStorage.getItem(this.cacheKey)

                if (state) {

                    this.activeMenu[ this.data.id ] = JSON.parse(state)

                }

                this.$watch(() => this.activeMenu[ this.data.id ], state => {

                    localStorage.setItem(this.cacheKey, state)

                })

            }

        },
        computed: {
            isTopCollapsible() {
                return this.data.expanded === null ? false : true && this.data.linkTo === null
            },
            isGroup() {
                return this.data.type === 'group'
            },
            isTopLevel() {
                return this.data.type === 'top_level'
            },
            cacheKey() {
                return `menu-state.${ this.data.id }`
            },
            isEmpty() {
                return this.data.resources.length === 0
            },
            topLevelLink() {

                if (this.data.linkTo) {

                    return {
                        is: 'router-link',
                        to: this.data.linkTo.router,
                        target: this.data.linkTo.target,
                        class: [ 'cursor-pointer', 'dim' ]
                    }

                }

                return {
                    is: 'h3'
                }

            }
        },
        methods: {
            toggleTopLevel() {
                if (this.isTopCollapsible) {
                    this.topExpanded = !this.topExpanded
                }
            },
            toggleGroup(id) {
                this.activeMenu[ id ] = !this.activeMenu[ id ]
            }
        }
    }

</script>

<style scoped>

    .top_level ul li:first-child {
        padding-top: 0;
    }

    .group ul li:first-child {
        padding-top: 1rem;
    }

    .group h4 {
        margin-left: 0;
    }

    .group h4:first-child {
        margin-top: 0;
    }

</style>
