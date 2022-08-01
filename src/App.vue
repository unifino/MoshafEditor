<template>
<div id="root" ref="root">

    <BG />
    <Screen />

</div>
</template>

// -- =====================================================================================

<script lang="ts">

// -- =====================================================================================

import { defineComponent, onMounted, ref }   from "vue";
import BG                               from "@/components/BG.vue"
import Screen                           from "@/components/Screen.vue"
import * as TS                          from "@/types/types"
import * as VX                          from "@/store/store";

// -- =====================================================================================

export default defineComponent ( {

    name: "App",

// -- =====================================================================================

    components: {
        BG,
        Screen
    },

// -- =====================================================================================

    setup () {

// -- =====================================================================================

        // eslint-disable-next-line
        const root = ref<HTMLElement>( null as any );

// -- =====================================================================================

        onMounted ( () => {

            const ua = navigator.userAgent;

            if ( /Android|webOS|Opera Mini|Mobile|mobile|CriOS/i.test(ua) )
                VX.store.dispatch( VX.Acts.clientDevice, TS.clientDeviceType.mobile );
            else if ( /iPhone|iPad|iPod|BlackBerry|IEMobile/i.test(ua) )
                VX.store.dispatch( VX.Acts.clientDevice, TS.clientDeviceType.mobile );
            else
                VX.store.dispatch( VX.Acts.clientDevice, TS.clientDeviceType.desktop );

            root.value.className = TS.clientDeviceType[ VX.store.getters.clientDevice ];

        } );

// -- =====================================================================================

        return { root }

    }

} );

// -- =====================================================================================

</script>

// -- =====================================================================================

<style>
</style>
