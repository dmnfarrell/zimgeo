---
layout: post
title:  "Plotting with Bokeh"
date:   2019-11-24 01:30:13 +0800
categories: code
tags: bokeh, geopandas
---

Bokeh is a javascript based plotting library for Python. It draws choropleth maps the way it would draw any polygons. First the geodataframe (with color data column added) is converted into a GeoJSONDataSource object which autotamically makes fields called ‘xs’ and ‘ys’ with the coordinates. We then plot the values as patches.

To get the data into Bokeh we read the shapefile. A shapefile is an vector data storage format for storing the attributes of geographic features. It is stored as a set of related files, usually placed together in zip format. This is easily read in Geopandas. Here we use a shapefile from naturalearthdata for all the countries in the world. It’s read in to a DataFrame like structure and the columns renamed.

{% highlight python linenos %}
import pandas as pd
import geopandas as gpd
import json
import matplotlib as mpl
import pylab as plt

from bokeh.io import output_file, show, output_notebook, export_png
from bokeh.models import ColumnDataSource, GeoJSONDataSource, LinearColorMapper, ColorBar
from bokeh.plotting import figure
from bokeh.palettes import brewer

def get_geodatasource(gdf):    
    """Get getjsondatasource from geopandas object"""
    json_data = json.dumps(json.loads(gdf.to_json()))
    return GeoJSONDataSource(geojson = json_data)

def bokeh_plot_map(geosource, data_col=None, columns=[], title='', palette='Blues',
                   label='', plot_width=600):
    """Plot bokeh map from GeoJSONDataSource """

    palette = brewer[palette][8]
    palette = palette[::-1]
    #Instantiate LinearColorMapper that linearly maps numbers in a range, into a sequence of colors.
    color_mapper = LinearColorMapper(palette = palette)#, low = 0, high = 100)

    color_bar = ColorBar(label_standoff=8,width = 500, height = 20,
                border_line_color=None,location = (0,0), orientation = 'horizontal')

    x = [(i, "@%s" %i) for i in columns]
    hover = HoverTool(
        tooltips=x, point_policy='follow_mouse')
    tools = ['wheel_zoom,pan,reset',hover]
    tile_provider = get_provider(Vendors.STAMEN_TERRAIN)
    p = figure(title = title, plot_width=plot_width, plot_height=int(plot_width*.9), toolbar_location = 'right', tools=tools)
    #p = figure(y_range=(-2411525,-1881111), x_range=(2799080,3722223),
    #           x_axis_type="mercator", y_axis_type="mercator", tools=tools)
    #p.add_tile(tile_provider)

    if data_col != None:
        color = {'field' :data_col , 'transform': color_mapper}
    else:
        color = 'gray'
    p.patches('xs','ys', source=geosource, line_width=0.8, line_color='black', fill_color=color, fill_alpha=1)
    p.circle(x='xs',y='ys',radius=10,source=geosource)
    p.toolbar.logo = None
    #p.xaxis.visible = False
    #p.yaxis.visible = False    
    #Specify figure layout.
    #p.add_layout(color_bar, 'below')
    return p

{% endhighlight %}

Use the code lke this:

{% highlight python linenos %}
landuse = gpd.read_file('data/historic_land_classes.shp')
geosource = get_geodatasource(landuse)
p = bokeh_plot_map(geosource, data_col='code', columns=['Area_Name','class'],
                    palette='Spectral', plot_width=800)
{% endhighlight %}

## Links

* https://docs.bokeh.org/en/latest/index.html
