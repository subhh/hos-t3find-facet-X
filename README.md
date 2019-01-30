# How extends find extension

## Basic architecture

All modifications of extension are realised in a new extension named `discovery`. 

### Facets

In original extension a couple of facet rendering pattern are available. For extending and creating new ones new partials will need. In this partials all parameters are realised with HTML5 data HTML entities. This pattern avoids Javascript inside the HTML code. The partials build a "bridge" between Typoscript and Javascript namespace.

#### Heatmap

Example for typoscript:
```
plugin.tx_find.settings.facets.20 {
   type = Heatmap
   fetchMaximum = 1000
   field = geoLocationPoint 
   tileProvider = provider=stamen&type=toner-lite&tile={z}/{x}/{y}{r}
   attribution = Map tiles by <a title="Design by Stamen Design" href="https://stamen.com/maps/">Stamen Design</a>, CC BY 3.0
```
This snippets tells the extension to look for `geolocationPoint` of solr and and to use some properties.

Content of ``/discovery/Resources/Private/Partials/Facets/Facet/Heatmap.html`:

```
<div class="facetMap-container">
  <div class="heatmapContainer"
        data-facetdata="{f:format.json(value:facetData.values)}"
        data-fetchmaximum="{facetInfo.fetchMaximum}" 
        data-tileprovider="{facetInfo.tileProvider}"
        data-attribution="{facetInfo.attribution}"
  </div>
</div>
```
`facetData` comes from controler (solr response data) and `facetInfo`comes from TS.

This partial generated a snippet like this:

```
<div class="facetMap-container">
  <div class="heatmapContainer"
        data-facetdata="{&quot;53.4635384,9.9674,17&quot;:26}"
        data-fetchmaximum="1000" 
        data-tileprovider="provider=stamen&type=toner-lite&tile={z}/{x}/{y}{r}">
  </div>
</div>
```

## ThreeStateSwitch

Content of ``/discovery/Resources/Private/Partials/Facets/Facet/ThreeStateswitch.html`:
```
<f:alias map="{valueOpen:'Open Access',labelOpen:'Open Access',labelAll:'Alle Dokumente',labelClose:'Restricted
Access',valueClose:'zugriffsbeschränkt',valueAll:'Open Access,zugriffsbeschränkt'}">

<f:for each="{config.activeFacets.OpenAccess}" as="item">
    <f:variable name="filter" value="{item.term}" />
</f:for>

<div class="tx_discovery_3stateswitch">
  <link href="typo3conf/ext/discovery/Resources/Public/CSS/toggle-switch.css" rel="stylesheet" />
  <style>.facets .facet-id-OpenAccess h1 {display:none}</style> 
  <div class="switch-toggle switch-3 switch-candy">

    <input id="on" name="state-d" type="radio"  <f:if
    condition="{filter} == {valueOpen}">checked="checked"</f:if>> 
    <label for="on">{labelOpen}</label>

    <input id="na" name="state-d" type="radio"  <f:if
    condition="{filter} =={valueAll}">checked="checked"</f:if>>
    <label for="na">{labelAll}</label>

    <input id="off" name="state-d" type="radio" <f:if
    condition="{filter} == 'zugriffsbeschränkt'">checked="checked"</f:if>>
    <label for="off">{labelClose}</label><a></a>

  </div>
  </div>
</f:alias>
```

