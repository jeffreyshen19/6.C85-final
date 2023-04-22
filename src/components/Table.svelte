<script>
    import * as d3 from "d3";

    const data = [
        {
            "country": "Guatemala",
            "departments": [
                {
                    "department_id": "GT16",
                    "department_name": "Alta Verapaz",
                    "food_insecurity": 0.6334164588528678,
                    "forest_loss": 0.2218713616972717
                },
                {
                    "department_id": "GT13",
                    "department_name": "Huehuetenango",
                    "food_insecurity": 0.4096109839816934,
                    "forest_loss": 0.04809782343648568
                },
                {
                    "department_id": "GT20",
                    "department_name": "Chiquimula",
                    "food_insecurity": 0.4641025641025641,
                    "forest_loss": 0.039392351642701766
                },
                {
                    "department_id": "GT12",
                    "department_name": "San Marcos",
                    "food_insecurity": 0.37373737373737376,
                    "forest_loss": 0.035570206265078924
                },
            ]
        },
        {
            "country": "Honduras",
            "departments": [
                {
                    "department_id": "901726",
                    "department_name": "Choluteca",
                    "food_insecurity": 0.5207253886010362,
                    "forest_loss": 0.03573317015144937
                },
                {
                    "department_id": "901730",
                    "department_name": "Cortés",
                    "food_insecurity": 0.5661764705882353,
                    "forest_loss": 0.1695066592008959
                },
                {
                    "department_id": "901732",
                    "department_name": "Francisco Morazán",
                    "food_insecurity": 0.4699738903394256,
                    "forest_loss": 0.14965109525895212
                },
                {
                    "department_id": "901742",
                    "department_name": "Yoro",
                    "food_insecurity": 0.5376623376623376,
                    "forest_loss": 0.1463831532429954
                },
            ]
        },
        {
            "country": "El Salvador",
            "departments": [
                {
                    "department_id": "1193",
                    "department_name": "San Salvador",
                    "food_insecurity": 0.5470085470085471,
                    "forest_loss": 0.028894402013940793
                },
                {
                    "department_id": "1184",
                    "department_name": "Ahuachapán",
                    "food_insecurity": 0.7080152671755725,
                    "forest_loss": 0.03822867625132158
                },
                {
                    "department_id": "1185",
                    "department_name": "Cabañas",
                    "food_insecurity": 0.6773162939297125,
                    "forest_loss": 0.03991922056438185
                },
                {
                    "department_id": "1197",
                    "department_name": "Usulután",
                    "food_insecurity": 0.5745098039215686,
                    "forest_loss": 0.056021037327214335
                }
            ]
        }
    ];

    export let visible;
    export let highlighted;
    let highlightedSet;
    $: highlightedSet = new Set(highlighted);

    let colorScaleForestLoss = d3.scaleSequential(d3.interpolateReds).domain([0, 0.25]);
    let colorScaleFoodInsecurity = d3.scaleSequential(d3.interpolateRgb("#fff", "#d33800")).domain([0, 0.75]);
</script>

<table style:opacity={visible ? 1 : 0}> 
    <tr>
        <th>Country</th>
        <th>Department</th>
        <th>Deforestation Rate</th>
        <th>Food Insecurity</th>
    </tr>
    {#each data as country, i}
        {#each country.departments as department, j}
            <tr style:opacity={highlightedSet.has(i) ? 1 : 0.1}>
                {#if j == 0}
                    <td rowspan="{country.departments.length}">{country.country}</td>
                {/if}
                <td>{department.department_name}</td>
                <td 
                    style:background-color="{colorScaleForestLoss(department.forest_loss)}"
                    style:border='none'
                    style:color={department.forest_loss > 0.1 ? "#fff":"#4d4d4d"}
                >
                    {(department.forest_loss * 100).toFixed(2)}%
                </td>
                <td 
                    style:background-color="{colorScaleFoodInsecurity(department.food_insecurity)}"
                    style:border='none'
                    style:color="#fff"
                >
                    {(department.food_insecurity * 100).toFixed(2)}%
                </td>
            </tr>
        {/each}
    {/each}

</table>

<style>

    table{
        width: 300px;
        position: absolute;
        top: 50%;
        left: 50%;
        transform: translate(-50%, -50%);
        width: 100%;
		max-width: 500px;
        transition: opacity 0.4s;
        border-spacing: 0;
        border-collapse: collapse;
    }

    tr{
        transition: opacity 0.4s;
    }

    td, th{
        font-family: Helvetica, Arial;
		font-weight: 200;
        color: #4d4d4d;
        
        text-align: left;
    }

    th{
        font-weight: bold;
        font-size: 0.9em;
        padding: 5px 10px 10px 10px;
    }

    td{
        border: 1px solid #f0f0f0;
        font-size: .8em;
        padding: 5px 10px;
    }


</style>