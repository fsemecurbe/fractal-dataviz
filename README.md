# fractal_dataviz

```
git clone

```

code pour les fichiers cartographiques

```
install spatial;
load spatial;

CREATE OR REPLACE VIEW filosofi AS from  read_parquet('https://www.data.gouv.fr/fr/datasets/r/b480cead-3f46-4b1b-a943-62a009b83f7a');

create or replace table carreaux as select *  from filosofi;
create index sindex on carreaux USING RTREE(geometry);

set variable size = 50000;


create or replace table carreaux_50000 as (
select x, y, sum(ind) as ind, ST_GeomFromText('POLYGON(('||x||' '||y||',
                                        '||x + getvariable('size')||' '||y||',  
                                        '||x + getvariable('size')||' '||y+getvariable('size')||',
                                        '||x||' '||y+getvariable('size')||',
                                        '||x||' '||y||'))') as geometry
from 
(select floor((ST_X((ST_Centroid(geometry)))-100) / getvariable('size')) * getvariable('size')  as x,
        floor((ST_Y((ST_Centroid(geometry)))-100) / getvariable('size')) * getvariable('size')  as y, ind
from carreaux
)
group by x,  y) 
;

COPY (carreaux_50000⟩ TO carreaux_50000.geojson'
WITH (FORMAT GDAL, DRIVER 'GeoJSON', SRS '3035');




```
