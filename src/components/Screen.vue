<template>
<div id="screen" ref="Screen" class="">

    <div class="buttonBox">
        <div class="button no_select" @click="shifter('forward')">→</div>
        <div class="button no_select" @click="confirm()" ref="confirmButton">✔</div>
        <div class="button no_select" @click="shifter('backward')">←</div>
    </div>

    <div class="divider"></div>

    <select class="sec" ref="sPc">
        <option v-for="(x,i) in 19" :key="i" :value="x"> {{cMap[i].join(" ")}} </option>
    </select>

    <div class="divider"></div>

    <textarea class="sec" ref="sP0"></textarea>
    <textarea class="sec" ref="sPa"></textarea>
    <textarea class="sec" ref="sP9"></textarea>

    <div class="divider"></div>

    <textarea class="sec" ref="sPb"></textarea>

    <div class="divider"></div>

    <textarea class="sec" ref="sPd"></textarea>
    <textarea class="sec" ref="sPn"></textarea>

</div>
</template>

// -- =====================================================================================

<script lang="ts">

import { defineComponent, ref, onMounted }  from "vue";
import Reported                         from "@/db/Reported.json";
import Modified                         from "@/db/Modified.json";

// -- =====================================================================================

export default defineComponent ( {

// -- =====================================================================================

    name: "Screen",

// -- =====================================================================================

    components: {

    },

// -- =====================================================================================

    setup () {


// -- =====================================================================================

        const cMap = [
        /*  0 */ [ "فاطِمَةُ الزهراء بِنتَ رَسُولِ اللّه", "عليها‌السلام" ],
        /*  1 */ [ "أبو الحسن اميرالمؤمنين الإمامُ علي", "عليه‌السلام" ],
        /*  2 */ [ "أبو محمد الإمام الحسن المجتبى", "عليه‌السلام" ],
        /*  3 */ [ "أبو عبدالله الإمام الحسين", "عليه‌السلام" ],
        /*  4 */ [ "أبو الحسن علي السجّاد الإمامُ زينُ العابدينَ", "عليه‌السلام" ],
        /*  5 */ [ "أبو جَعفَرٍ محمد الإمامُ الباقر", "عليه‌السلام" ],
        /*  6 */ [ "أبو عَبدِاللّهَ الإمامُ الصّادقُ", "عليه‌السلام" ],
        /*  7 */ [ "أبو إبراهيم الإمام مُوسَى بنِ جَعفَر الكاظم", "عليه‌السلام" ],
        /*  8 */ [ "أبو الحَسَن الإمامُ علي بن موسى الرِّضا", "عليه‌السلام" ],
        /*  9 */ [ "أبو جعفر الإمام محمد التقي الجواد", "عليه‌السلام" ],
        /* 10 */ [ "ابُوالحَسَنِ الثّالِث الإمام الهادي النقي", "عليه‌السلام" ],
        /* 11 */ [ "الإمامُ الحسن العسكريُّ", "عليه‌السلام" ],
        /* 12 */ [ "أبو القاسم الإمام المهدي الحُجَّهُ اللّه", "عليه‌السلام" ],
        /* 13 */ [ "أَبُو القَاسِم مُحَمَّد رسولُ اللّه", "صلى‌الله‌عليه‌وآله‌وسلم" ],
        /* 14 */ [ "حضرت عيسى", "عليه‌السلام" ],
        /* 15 */ [ "الخضرُ", "عليه‌السلام" ],
        /* 16 */ [ "لُقمانُ", "عليه‌السلام" ],
        /* 17 */ [ "حضرت مُوسى", "عليه‌السلام" ],
        /* 18 */ [ "حدیث قدسی", "" ],
        /* 19 */ [ "", "عليه‌السلام" ]
    ];

// -- =====================================================================================

        // eslint-disable-next-line
        const Screen = ref<HTMLElement>( null as any );
        // eslint-disable-next-line
        const sP0 = ref<HTMLElement>( null as any );
        // eslint-disable-next-line
        const sP9 = ref<HTMLElement>( null as any );
        // eslint-disable-next-line
        const sPa = ref<HTMLElement>( null as any );
        // eslint-disable-next-line
        const sPb = ref<HTMLElement>( null as any );
        // eslint-disable-next-line
        const sPc = ref<HTMLElement>( null as any );
        // eslint-disable-next-line
        const sPd = ref<HTMLElement>( null as any );
        // eslint-disable-next-line
        const sPn = ref<HTMLElement>( null as any );
        // eslint-disable-next-line
        const confirmButton = ref<HTMLElement>( null as any );

// -- =====================================================================================

        let RPT = Reported;

        // .. remove duplicates
        RPT = Reported.reduce( (soFar, next) => {
            if ( !soFar.find( x => x.n === next.n ) ) soFar.push( next );
            return soFar;
        }, [ RPT[0] ] );

        const MDF = Modified;

        // .. update data by MDF
        for ( const x of MDF )
            RPT[ RPT.findIndex( f => f.n === x.n ) ] = x as any;
        // .. restrict data by MDF
        for ( const x of MDF )
            RPT.splice( RPT.findIndex( f => f.n === x.n ), 1 );

        let idx = 0;
        let data = RPT[ idx ];

// -- =====================================================================================

        function styler ( text: string ): string {

            const salam = [
                "عليها‌السلام",
                "عليه‌السلام",
                "صلى‌الله‌عليه‌وآله‌وسلم",
                "عليهما‌السلام",
                "عليهم‌السلام",
                "رحمه‌الله",
                "رحمهم‌الله",
                "رضي‌الله‌عنه",
                "عزوجل"
            ];

            const alaem = [ "(", ")" ];

            for ( const x of salam ) {
                const re = new RegExp(x, 'g');
                text = text.replace( re, '<div>'+x+'</div>' );
            }

            return text;

        }

// -- =====================================================================================

        function Setter () {

            ( sP0.value as any ).value = data[0] || "";
            ( sP9.value as any ).value = data[9] || "";
            ( sPa.value as any ).value = data.a;
            ( sPb.value as any ).value = data.b || "";
            // .. handling not declared c_map code
            if ( typeof data.c === "undefined" ) data.c = -1;
            ( sPc.value as any ).selectedIndex = data.c;
            ( sPd.value as any ).value = data.d || "";
            ( sPn.value as any ).value = data.n;

            confirmButton.value.style.backgroundColor =
                MDF.find( x => x.n === data.n ) ? "green" : "black"

        }

// -- =====================================================================================

        function adjuster () {
            for ( const e of [ sP0, sP9, sPa, sPb, sPd, sPn ] ) {
                e.value.style.height = "1px";
                e.value.style.height = e.value.scrollHeight + "px";
            }
        }

// -- =====================================================================================

        const shifter = function ( action: "forward"| "backward" ) {

            switch (action) {
                case "forward": idx < RPT.length-1 ? idx++ : alert("end"); break;
                case "backward": idx > 0 ? idx-- : alert("start"); break;
            }

            data = RPT[ idx ];

            Setter();
            adjuster();

        }

// -- =====================================================================================

        function confirm () {

            data[0] = ( sP0.value as any ).value.trim();
            data[9] = ( sP9.value as any ).value.trim();
            data.a  = ( sPa.value as any ).value.trim();
            data.b  = ( sPb.value as any ).value.trim();
            data.c  = ( sPc.value as any ).selectedIndex;
            data.d  = ( sPd.value as any ).value.trim();
            data.n  = parseInt( ( sPn.value as any ).value );

            // .. register Unique modification
            const pID = MDF.findIndex( x => x.n === data.n );
            if ( ~pID ) MDF[ pID ] = data as any;
            else MDF.push( data as any );

            // .. LED on
            confirmButton.value.style.backgroundColor = "green";

            // .. export result
            console.log( MDF );

        }

// -- =====================================================================================

        onMounted ( () => {

            Setter();
            adjuster();

        } );

// -- =====================================================================================

        return {
            Screen,
            sP0, sP9, sPa, sPb, sPc, sPd, sPn,
            cMap,
            shifter,
            confirm, confirmButton
        }

    }

// -- =====================================================================================

} );

// -- =====================================================================================

</script>

// -- =====================================================================================

<style scoped>

/*                                                                                       */

#screen {
    /* box-shadow          : 0 0 4em 0 #02090f; */
    position            : relative;
    margin              : 10px auto;
    border-radius       : 10px;
    /* background-color    : #032f41; */
    padding             : 30px;
    direction           : rtl;
}

.desktop #screen {
    height              : 90vw;
    width               : 90vw;
}

.mobile #screen {
    height              : 63vw;
    width               : 90vw;
    bottom              : 15vw;
}

.minimize {
    animation           : minimize .5s;
    animation-fill-mode : both;
}

.maximize {
    animation           : maximize .3s;
    animation-fill-mode : both;
}

@keyframes minimize {
    0%  { transform     : scale(1)          }
    40% { transform     : scale(1.1)        }
    100%{ transform     : scale(.5)         }
}

@keyframes maximize {
    0%  { transform     : scale(.5)         }
    100%{ transform     : scale(1)          }
}

.sec {
    border              : solid #e6e6e6 1px;
    border-radius       : 7px;
    margin              : 3px auto;
    padding             : 5px 10px;
    width               : 97%;
    height              : auto
}

.divider {
    border-radius: 7px;
    background-color: #1a528f;
    height: 2px;
    margin: 14px 20px;
}

.buttonBox{
    margin: 20px auto;
    width: 330px;

}

.button {
    line-height: 19px;
    font-size: 40px;
    width: 90px;
    height: 26px;
    border-radius: 7px;
    background-color: #02090f;
    text-align: center;
    padding: 4px;
    margin: 5px;
    cursor: pointer;
    display: inline-block;
}

/*                                                                                       */

</style>
