<header>
    <div>
        <h1>The Impact of Deforestation on Central American Migration</h1><br>
        <p>By Anne Liu, Jeffrey Shen, Yun Shwe Eain</p>
    </div>
</header>

<p class = "attribution">Image courtesy of <a href = "https://news.mongabay.com/2019/04/indigenous-leaders-decry-colombias-deadly-crackdown-on-land-protesters/"><i>Mongabay</i></a>.</p>

<div class = "paragraph" id = "intro">
    <img src={"/images/deforestation_intro.jpeg"} alt="Deforestation" />
    <p style:font-size="0.8em" style:text-align="right" style:margin-bottom="0.5em">Douglas Magno/AFP/Getty Images</p>
    <h2>Introduction</h2>
    <p>Deforestation has led to increased Central American migration rates. In the past few decades, forestry coverage in El Salvador, Guatemala and Honduras has decreased drastically, which negatively impacted residents’ livelihood, food security and their susceptibility towards natural disasters, such as floods and droughts. Regions with severe deforestation coincide with regions with high migration rates, and as livelihood and food security are commonly ranked as top motivations for migration, people living in heavily deforested locations are also more likely to migrate due to natural disasters. As more people migrate or are displaced externally or internally to urban centers, regional governments are faced with enhanced pressure with rural migrants.</p>    
    <br>
    <p><i>This data visualization was made with data contributed by the United Nations World Food Programme (WFP).</i></p>
</div>

<style>

    .attribution{
        text-align: right;
        padding: 5px 10px;
    }

    .attribution a{
        color: #524eb7;
        text-decoration: none;
    }

    header{
		height:100vh;
        width:100vw;
        position: relative;
        z-index: 500001;
    }

    header::before {
        content: "";
        position: absolute;
        top: 0; left: 0;
        width: 100%; height: 100%;
        background-image: url("/images/intro.jpg");
        filter: grayscale(70%) brightness(0.7);
    }

    header h1{
        font-size: 3.5em;
        line-height: 1.25em;
        font-family: "Georgia";
        letter-spacing: 2.5px;
        font-weight: bolder;
        margin-bottom: 0;
    }

    header p{
        font-size: 1.2em;
    }

    header div{
        position: absolute;
        top: 50%;
        left: 50%;
        transform: translate(-50%, -50%);
        width: 90vw;
        text-align: center;
        color: #eee;
    }

    :global(.paragraph) {
        width: 600px;
        margin: 100px auto;
        text-align: justify;
        font-size: 18px;
        line-height: 1.35em;
    }

    .paragraph img{
        width: 100%;
        margin-bottom: 0em;
    }
</style>