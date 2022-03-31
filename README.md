# timeseries_utilities

### Generating sinthetic time series data
```
#pip install "gluonts~=0.8" ujson git+https://github.com/aws-samples/smallmatter-package@main#egg=smallmatter

sku_count = 150
assert isinstance(sku_count, int) and (sku_count > 1), \
    "sku_count must be a positive integer"

from gluonts.dataset.artificial import ComplexSeasonalTimeSeries
from gluonts_nb_utils.generate_synthetic import generate_daily_csv

# ComplexSeasonalTimeSeries starts on Thu 28-Nov-2013.
# - Next SUN is 1-Dec-2013.
# - Next MON is 2-Dec-2013.
# - 1st week ends on 1-Dec-2013 (W-SUN, aligned to ending SUN).
# - Subsequent seeks: 2-8/Dec/2013, 9-15/Dec/2013, ...

length_low = 400
length_high = int(length_low * 1.25)

artificial_dataset=ComplexSeasonalTimeSeries(
    num_series=sku_count,
    prediction_length=7,
    freq_str="D",
    length_low=length_low,
    length_high=length_high,
    min_val = 0,
    is_integer=True,
    is_noise=True,
    is_scale=True,
    percentage_unique_timestamps=0.0,
)

generate_daily_csv(
    file_name='../data/input_to_forecast.csv',   #path para guardar el csv
    artificial_dataset=artificial_dataset,
    colnames=["sku", "timestamp", "quantity"],
    ts_prefix="item_",
)

# Display statistics of generated timeseries.
print('Statistics of generated timeseries:')
import pandas as pd
df = pd.read_csv('../data/input_to_forecast.csv', low_memory=False, parse_dates=['timestamp'], infer_datetime_format=True)  # cambia por el nombre del path arriba

display(df.groupby('sku').agg({'timestamp': ['min', 'max', 'count']}))
```
