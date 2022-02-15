# page-visit-funnel
Analyse of website data (purchase funnel)
#Page Visits Funnel
import codecademylib3
import pandas as pd

visits = pd.read_csv('visits.csv',
                     parse_dates=[1])
cart = pd.read_csv('cart.csv',
                   parse_dates=[1])
checkout = pd.read_csv('checkout.csv',
                       parse_dates=[1])
purchase = pd.read_csv('purchase.csv',
                       parse_dates=[1])

#1.inspecting the data frames
print(visits.head(5))
print(cart.head(5))
print(checkout.head(5))
print(purchase.head(5))

#2.Combining visits and cart df

visits_cart = pd.merge(visits, cart, how = 'left')
print(visits_cart)

#3.how long the merged df? - 2000 rows
#print(visits_cart.user_id.count())

visits_cart_rows = len(visits_cart)

#3.how many of the timestamps are null for the column cart_time? - 1652 nulls
null_cart_times = len(visits_cart[visits_cart.cart_time.isnull()])
print(null_cart_times)

#5.percentage of users who didn't place a good into their cart - 0,82%
percentage_not_using_carts_customers = float(null_cart_times / visits_cart_rows)
print(percentage_not_using_carts_customers)

#6.merge cart and checkout. Counting nulls (122). Percentage of the useres who didn't proceed to checkout - 0.25%
cart_checkout = pd.merge(cart, checkout, how = 'left')
print(cart_checkout)
cart_checkout_rows = len(cart_checkout)
null_checkout_times = len(cart_checkout[cart_checkout.checkout_time.isnull()])
print(null_checkout_times)
print(float(null_checkout_times / cart_checkout_rows))

#7.merge all 4 steps of the funnel
all_data = visits\
.merge(cart, how = 'left')\
.merge(checkout, how = 'left')\
.merge(purchase, how = 'left')
print(all_data.head())

#8.percentage of the users who proceeded to checkout, but didn't buy a product - 0,168
checkout_purchase = pd.merge(checkout, purchase, how = 'left')

checkout_purchase_rows = len(checkout_purchase)
null_purchase_times = len(checkout_purchase[checkout_purchase.purchase_time.isnull()])
print(null_purchase_times)
print(float( null_purchase_times/ checkout_purchase_rows))

#The first step of the funnel is the weakest - 82% of clients didn't even put a product in their cart. The company should change their website to make a buying process wishable by clients, more attractive. They also should check their price policy, discount policy (maybe, to offer the discount for the first purchase on the site etc)

#10.The AVG time from initial visit to final purchase - 43.53 min 
all_data['avg_time'] = all_data['purchase_time'] - all_data['visit_time']
print(all_data)
print(all_data.avg_time.head(10))
print(all_data.avg_time.mean())

