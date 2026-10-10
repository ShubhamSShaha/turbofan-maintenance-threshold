# When Should You Service the Engine?

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/ShubhamSShaha/turbofan-maintenance-threshold/blob/main/when_to_service_the_engine.ipynb)

Moving from a fixed maintenance schedule to condition-based servicing on 100 jet engines, using one sensor and a cost model.

**Business problem:** An airline services every engine after 150 flights, healthy or not. Some engines fail before then, which is expensive. Most are still fine at 150, so the airline throws away good flying time. Is there a better rule?

**Approach:** Every engine has a sensor for the temperature of the gas leaving the turbine (T50). A worn engine runs hotter. The new rule is: *service the engine when its 10-flight average temperature reaches a trigger value.* For each candidate trigger, I simulate all 100 engines and price the outcome:

| Outcome | Cost (illustrative) |
|---|---|
| Serviced before it broke | $1M |
| Broke in service | $5M |
| Each good flight wasted by servicing early | $0.02M |

![Turbine outlet temperature rises as engines wear out](images/engine_temperatures.png)

## Results

| Rule | Engines that broke | Avg. good flights wasted | Total cost (100 engines) |
|---|---|---|---|
| Today: service every 150 flights | 7 | 61 | **$242M** |
| Service at **517.75 °C** (10-flight average) | 2 | 9 | **$125M** |

**The temperature trigger cuts total cost by about $117M (48%)** while also reducing breakdowns from 7 to 2.

**The best trigger depends on the business, not just the data.** With the same engines and the same sensor, only the cost of a breakdown changes:

| Breakdown cost | Cheapest trigger | Engines that broke | Total cost | vs. today's schedule |
|---|---|---|---|---|
| $1.5M | 518.75 °C | 14 | $117M | $218M |
| $5M | 517.75 °C | 2 | $125M | $242M |
| $20M | 516.75 °C | 0 | $128M | $347M |

![Total cost by trigger temperature for three breakdown costs](images/cost_by_trigger.png)

The more a failure costs, the earlier you service. That decision belongs to the business, and the analysis makes it explicit.

**Averaging matters.** Using single-flight readings instead of the 10-flight average, the best rule costs $129M instead of $125M. Noisy readings trigger services too early.

## Limitations

- Costs are illustrative, not real airline figures.
- The trigger is chosen and evaluated on the same 100 engines. A production version would pick it on one set of engines and test it on another.
- One sensor and one threshold. Real condition-based maintenance combines many sensors, often with a remaining-useful-life model.

## How it's built

- **Data:** NASA C-MAPSS turbofan engine degradation simulation, subset FD001 (Saxena, Goebel, Simon & Eklund, 2008). T50 is converted from °Rankine to °C and smoothed with a 10-flight rolling mean. The data is embedded in the notebook, so it runs with no downloads.
- **Tools:** Python, pandas, NumPy, matplotlib
- **Format:** an interactive notebook. You choose a trigger, see its cost, then see how the best answer moves when the cost of failure changes.

Built as the hands-on exercise for a guest session I led with VIT's School of Mechanical Engineering ("From Technology to Business Impact: Building an Analytics-Driven Mindset", October 2026).

## Run it

Click **Open in Colab** above and run the cells in order, or run it locally:

```bash
pip install pandas numpy matplotlib jupyter
jupyter notebook when_to_service_the_engine.ipynb
```
