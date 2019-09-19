# Segmenting and Clustering Neighborhoods in Toronto

## Download our excel data created from the wikipedia page


```python
import pandas as pd

#Initial Data
df = pd.read_csv('Postcode Toronto.csv')
df.head() #Initial Data
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Postcode</th>
      <th>Borough</th>
      <th>Neighbourhood</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>M1A</td>
      <td>Not assigned</td>
      <td>Not assigned</td>
    </tr>
    <tr>
      <td>1</td>
      <td>M2A</td>
      <td>Not assigned</td>
      <td>Not assigned</td>
    </tr>
    <tr>
      <td>2</td>
      <td>M3A</td>
      <td>North York</td>
      <td>Parkwoods</td>
    </tr>
    <tr>
      <td>3</td>
      <td>M4A</td>
      <td>North York</td>
      <td>Victoria Village</td>
    </tr>
    <tr>
      <td>4</td>
      <td>M5A</td>
      <td>Downtown Toronto</td>
      <td>Harbourfront</td>
    </tr>
  </tbody>
</table>
</div>



## Question 1: Create a table with particular features

### Feature one: Only process the cells that have an assigned borough. Ignore cells with a borough that is Not assigned.


```python
#These are the Data that do not have 'Not aasigned' in Borough
df_1 = df[df["Borough"]!='Not assigned']
n = df_1.shape[0]
```

### Feature two : If a cell has a borough but a 'Not assigned' Neighborhood, then the neighborhood will be the same as the borough.


```python
#Data that had Not aasigned in the Neighborhood column and replace them with the information they had in the Borough column
df_2 = df_1[df_1['Neighbourhood']=='Not assigned']
df_2['Neighbourhood'].replace('Not assigned',df_2['Borough'], inplace=True)

# We remove those that meet that in the Neighborhood column = Not assigned. and by df_2 we know that it is only one element
Num = df_1[df_1['Neighbourhood']=='Not assigned'].index.values.item()
df_1.drop(Num, axis=0, inplace =True)
```

### We create the table that meets the above conditions


```python
# We put together the df_1 and df_2 to have all the conditions related to Not assigned
df_3 = df_1.append(df_2,ignore_index=True)
N = df_3.shape[0]
df_3.head() 
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Postcode</th>
      <th>Borough</th>
      <th>Neighbourhood</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>M3A</td>
      <td>North York</td>
      <td>Parkwoods</td>
    </tr>
    <tr>
      <td>1</td>
      <td>M4A</td>
      <td>North York</td>
      <td>Victoria Village</td>
    </tr>
    <tr>
      <td>2</td>
      <td>M5A</td>
      <td>Downtown Toronto</td>
      <td>Harbourfront</td>
    </tr>
    <tr>
      <td>3</td>
      <td>M5A</td>
      <td>Downtown Toronto</td>
      <td>Regent Park</td>
    </tr>
    <tr>
      <td>4</td>
      <td>M6A</td>
      <td>North York</td>
      <td>Lawrence Heights</td>
    </tr>
  </tbody>
</table>
</div>



### Feature three : More than one neighborhood can exist in one postal code area. These rows will be combined into one row with the neighborhoods separated with a comma

### To solve this feature we will do the following:

#### We create a table with the account of each different postal code


```python
#We have a table call 'postal' that tells us how many elements there are for each different postal code
postal = df_3['Postcode'].value_counts().to_frame()
A = postal.shape[0]
B = list(range(0,A,1))

postal['S'] = B
postal['Postcodename']=postal.index
postal.set_index('S', inplace=True)
maxi = postal['Postcode'].max()
```

### In two empty lists, called 'Caja1' and 'Caja2', we put the elements of the table that meet the conditions that have the repetition of the postal code greater than 1 or only have a one postal code


```python
# Lists that include postal codes that are repeated more than once and those that do not
Caja1 = []
Caja2 = []
for i in range(0,A):
    if postal.loc[i,'Postcode'] > 1:
        Caja1.append(postal.loc[i,'Postcodename'])

for i in range(0,A):
    if postal.loc[i,'Postcode'] == 1:
        Caja2.append(postal.loc[i,'Postcodename'])
```

### We transform those lists to tables (df_4, df_5)


```python
#Now we have a table that returns the postal codes that appear more than once in the data
df_4 = pd.DataFrame(Caja1)
df_4.rename(columns={0: 'Code'}, inplace = True)
J = df_4.shape[0]

#Now we have a table that returns the postal codes that appear only once in the data
df_5 = pd.DataFrame(Caja2)
df_5.rename(columns={0: 'Code'}, inplace = True)
P = df_5.shape[0]
```

### It is time to create the table that shows only the elements that have their postal code repeated


```python
# We create the table called table1 which contains all the elements that have their postal code repeated at least twice
Vacio1 = []
tabla1 = pd.DataFrame(Vacio1)

for i in range(0,N):
    for j in range(0,J):
        if Caja1[j] == df_3.loc[i,'Postcode']:
            tabla1 = tabla1.append(df_3.loc[i,].to_frame().transpose(), ignore_index=True)
        
W = tabla1.shape[0]
tabla1.head() 
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Postcode</th>
      <th>Borough</th>
      <th>Neighbourhood</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>M5A</td>
      <td>Downtown Toronto</td>
      <td>Harbourfront</td>
    </tr>
    <tr>
      <td>1</td>
      <td>M5A</td>
      <td>Downtown Toronto</td>
      <td>Regent Park</td>
    </tr>
    <tr>
      <td>2</td>
      <td>M6A</td>
      <td>North York</td>
      <td>Lawrence Heights</td>
    </tr>
    <tr>
      <td>3</td>
      <td>M6A</td>
      <td>North York</td>
      <td>Lawrence Manor</td>
    </tr>
    <tr>
      <td>4</td>
      <td>M1B</td>
      <td>Scarborough</td>
      <td>Rouge</td>
    </tr>
  </tbody>
</table>
</div>



### It is time to create the table that shows only the elements that do NOT have their postal code repeated


```python
# We create the table called table2 which contains all the elements that have their postal code unique
Vacio2 = []
tabla2 = pd.DataFrame(Vacio2)
for i in range(0,N):
    for j in range(0,P):
        if Caja2[j] == df_3.loc[i,'Postcode']:
            tabla2 = tabla2.append(df_3.loc[i,].to_frame().transpose(), ignore_index=True)

K = tabla2.shape[0]
tabla2.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Postcode</th>
      <th>Borough</th>
      <th>Neighbourhood</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>M3A</td>
      <td>North York</td>
      <td>Parkwoods</td>
    </tr>
    <tr>
      <td>1</td>
      <td>M4A</td>
      <td>North York</td>
      <td>Victoria Village</td>
    </tr>
    <tr>
      <td>2</td>
      <td>M9A</td>
      <td>Etobicoke</td>
      <td>Islington Avenue</td>
    </tr>
    <tr>
      <td>3</td>
      <td>M3B</td>
      <td>North York</td>
      <td>Don Mills North</td>
    </tr>
    <tr>
      <td>4</td>
      <td>M6B</td>
      <td>North York</td>
      <td>Glencairn</td>
    </tr>
  </tbody>
</table>
</div>



### Since we have both tables it will be necessary to concatenate the elements that have the same postal code

### For that, a table is created for each type of repetition, i.e. a table for those that are repeated only 2 times, for those that are repeated only 3 times and thus up to the maximum of repetitions that in this case are 8 times


```python
#Creating the tables containing the postal codes that are repeated the same number of times
Vacio3 = [] 
C1 = tabla1.copy()

#Creating a table containing only the postal codes that are repeated twice
Po2 = postal[postal['Postcode']== 2]
D2 = Po2.shape[0]

Vector2 = Po2['Postcodename']
Lista2 = list(Vector2)
Tabla2 = pd.DataFrame(Vacio3)

for i in range(0,W):
    for j in range(0,D2):
        if Lista2[j] == C1.loc[i,'Postcode']:
            Tabla2 = Tabla2.append(C1.loc[i,].to_frame().transpose(), ignore_index=True)

#Creating a table containing only the postal codes that are repeated three times
Po3 = postal[postal['Postcode']== 3]
D3 = Po3.shape[0]

Vector3 = Po3['Postcodename']
Lista3 = list(Vector3)
Tabla3 = pd.DataFrame(Vacio3)

for i in range(0,W):
    for j in range(0,D3):
        if Lista3[j] == C1.loc[i,'Postcode']:
            Tabla3 = Tabla3.append(C1.loc[i,].to_frame().transpose(), ignore_index=True)

#Creating a table containing only the codes that are repeated four times
Po4 = postal[postal['Postcode']== 4]
D4 = Po4.shape[0]

Vector4 = Po4['Postcodename']
Lista4 = list(Vector4)
Tabla4 = pd.DataFrame(Vacio3)

for i in range(0,W):
    for j in range(0,D4):
        if Lista4[j] == C1.loc[i,'Postcode']:
            Tabla4 = Tabla4.append(C1.loc[i,].to_frame().transpose(), ignore_index=True)

#Creating a table containing only the codes that are repeated five times
Po5 = postal[postal['Postcode']== 5]
D5 = Po5.shape[0]

Vector5 = Po5['Postcodename']
Lista5 = list(Vector5)
Tabla5 = pd.DataFrame(Vacio3)

for i in range(0,W):
    for j in range(0,D5):
        if Lista5[j] == C1.loc[i,'Postcode']:
            Tabla5 = Tabla5.append(C1.loc[i,].to_frame().transpose(), ignore_index=True)

#Creating a table containing only the codes that are repeated seven times (There are no postal codes that are repeated six times)
Po7 = postal[postal['Postcode']== 7]
D7 = Po7.shape[0]

Vector7 = Po7['Postcodename']
Lista7 = list(Vector7)
Tabla7 = pd.DataFrame(Vacio3)

for i in range(0,W):
    for j in range(0,D7):
        if Lista7[j] == C1.loc[i,'Postcode']:
            Tabla7 = Tabla7.append(C1.loc[i,].to_frame().transpose(), ignore_index=True)
            
#Creating a table containing only the codes that are repeated eight times (There are no postal codes that are repeated more than eight times)
Po8 = postal[postal['Postcode']== 8]
D8 = Po8.shape[0]

Vector8 = Po8['Postcodename']
Lista8 = list(Vector8)
Tabla8 = pd.DataFrame(Vacio3)

for i in range(0,W):
    for j in range(0,D8):
        if Lista8[j] == C1.loc[i,'Postcode']:
            Tabla8 = Tabla8.append(C1.loc[i,].to_frame().transpose(), ignore_index=True) 
```

### Since we have those tables: with the following iterations, the elements of the Neighborhood columns are concatenated and added to a different table for each case


```python
#We are going to get all the tables with the elements of the concatenated neighborhood columns and without repetitions
Vacio4 = []

#We get the table without the repeated postal codes of the case in which they were repeated only 2 times
C2 = Tabla2.copy()
T2 = C2.shape[0]
Buena2 = pd.DataFrame(Vacio4)

for i in range(0,T2-1):
    if Tabla2.loc[i,'Postcode'] == Tabla2.loc[i+1,'Postcode']:
        C2.loc[i,'Neighbourhood'] = C2.loc[i,'Neighbourhood'] + ',' + ' ' + C2.loc[i+1,'Neighbourhood']
        Buena2 = Buena2.append(C2.loc[i,], ignore_index=True)

#We get the table without the repeated postal codes of the case in which they were only repeated 3 times
C3 = Tabla3.copy()
T3 = C3.shape[0]
Buena3 = pd.DataFrame(Vacio4)

for i in range(0,T3-2):
    if Tabla3.loc[i,'Postcode'] == Tabla3.loc[i+1,'Postcode'] == Tabla3.loc[i+2,'Postcode']:
        C3.loc[i,'Neighbourhood'] = C3.loc[i,'Neighbourhood'] + ',' + ' ' + C3.loc[i+1,'Neighbourhood'] + ',' + ' ' + C3.loc[i+2,'Neighbourhood']
        Buena3 = Buena3.append(C3.loc[i,], ignore_index=True)
        
#We get the table without the repeated postal codes of the case in which they were repeated only 4 times
C4 = Tabla4.copy()
T4 = C4.shape[0]
Buena4 = pd.DataFrame(Vacio4)

for i in range(0,T4-3):
    if Tabla4.loc[i,'Postcode'] == Tabla4.loc[i+1,'Postcode'] == Tabla4.loc[i+2,'Postcode'] == Tabla4.loc[i+3,'Postcode']:
        C4.loc[i,'Neighbourhood'] = C4.loc[i,'Neighbourhood'] + ',' + ' ' + C4.loc[i+1,'Neighbourhood'] + ',' + ' ' + C4.loc[i+2,'Neighbourhood'] + ',' + ' ' + C4.loc[i+3,'Neighbourhood']
        Buena4 = Buena4.append(C4.loc[i,], ignore_index=True)

#We get the table without the repeated postal codes of the case in which they were repeated only 5 times
C5 = Tabla5.copy()
T5 = C5.shape[0]
Buena5 = pd.DataFrame(Vacio4)

for i in range(0,T5-4):
    if Tabla5.loc[i,'Postcode'] == Tabla5.loc[i+1,'Postcode'] == Tabla5.loc[i+2,'Postcode'] == Tabla5.loc[i+3,'Postcode'] == Tabla5.loc[i+4,'Postcode']:
        C5.loc[i,'Neighbourhood'] = (C5.loc[i,'Neighbourhood'] + ',' + ' ' + C5.loc[i+1,'Neighbourhood'] + ',' + ' ' + C5.loc[i+2,'Neighbourhood'] + ',' + ' ' + C5.loc[i+3,'Neighbourhood'] 
                                     + ',' + ' ' + C5.loc[i+4,'Neighbourhood'])
        Buena5 = Buena5.append(C5.loc[i,], ignore_index=True)

#We get the table without the repeated postal codes of the case in which they were only repeated 7 times
C7 = Tabla7.copy()
T7 = C7.shape[0]
Buena7 = pd.DataFrame(Vacio4)

for i in range(0,T7-6):
    if (Tabla7.loc[i,'Postcode'] == Tabla7.loc[i+1,'Postcode'] == Tabla7.loc[i+2,'Postcode'] == Tabla7.loc[i+3,'Postcode'] == Tabla7.loc[i+4,'Postcode'] 
        == Tabla7.loc[i+5,'Postcode'] == Tabla7.loc[i+6,'Postcode']):
        C7.loc[i,'Neighbourhood'] = (C7.loc[i,'Neighbourhood'] + ',' + ' ' + C7.loc[i+1,'Neighbourhood'] + ',' + ' ' + C7.loc[i+2,'Neighbourhood'] + ',' + ' ' + C7.loc[i+3,'Neighbourhood']
         + ',' + ' ' + C7.loc[i+4,'Neighbourhood'] + ',' + ' ' + C7.loc[i+5,'Neighbourhood'] + ',' + ' ' + C7.loc[i+6,'Neighbourhood'])
        Buena7 = Buena7.append(C7.loc[i,], ignore_index=True)
    
#We get the table without the repeated postal codes of the case in which they were only repeated 8 times
C8 = Tabla8.copy()
T8 = C8.shape[0]
Buena8 = pd.DataFrame(Vacio4)

for i in range(0,T8-7):
    if (Tabla8.loc[i,'Postcode'] == Tabla8.loc[i+1,'Postcode'] == Tabla8.loc[i+2,'Postcode'] == Tabla8.loc[i+3,'Postcode'] == Tabla8.loc[i+4,'Postcode'] 
        == Tabla8.loc[i+5,'Postcode'] == Tabla8.loc[i+6,'Postcode'] == Tabla8.loc[i+7,'Postcode']):
        C8.loc[i,'Neighbourhood'] = (C8.loc[i,'Neighbourhood'] + ',' + ' ' + C8.loc[i+1,'Neighbourhood'] + ',' + ' ' + C8.loc[i+2,'Neighbourhood'] + ',' + ' ' + C8.loc[i+3,'Neighbourhood']
         + ',' + ' ' + C8.loc[i+4,'Neighbourhood'] + ',' + ' ' + C8.loc[i+5,'Neighbourhood'] + ',' + ' ' + C8.loc[i+6,'Neighbourhood'] + ',' + ' ' + C8.loc[i+7,'Neighbourhood'])
        Buena8 = Buena8.append(C8.loc[i,], ignore_index=True)
```

### Finally, since we have all the concatenated and clean tables, it would only be necessary to join them to obtain the final table with all the requested features


```python
final1 = tabla2.append(Buena2,ignore_index=True, sort =True)
final2 = final1.append(Buena3, ignore_index=True, sort =True)
final3 = final2.append(Buena4, ignore_index=True, sort =True)
final4 = final3.append(Buena5, ignore_index=True, sort =True)
final5 = final4.append(Buena7, ignore_index=True, sort =True)

#Fixing the last details in the final dataframe
final_df = final5.append(Buena8, ignore_index=True, sort =True)
final_df.rename(columns={'Postcode' : 'PostalCode'}, inplace = True)
cols = final_df.columns.tolist()
cols = cols[-1:] + cols[:-1]
final_df = final_df[cols]
final_df.rename(columns={'Neighbourhood': 'Neighborhood'},inplace = True )
final_df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>PostalCode</th>
      <th>Borough</th>
      <th>Neighborhood</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>M3A</td>
      <td>North York</td>
      <td>Parkwoods</td>
    </tr>
    <tr>
      <td>1</td>
      <td>M4A</td>
      <td>North York</td>
      <td>Victoria Village</td>
    </tr>
    <tr>
      <td>2</td>
      <td>M9A</td>
      <td>Etobicoke</td>
      <td>Islington Avenue</td>
    </tr>
    <tr>
      <td>3</td>
      <td>M3B</td>
      <td>North York</td>
      <td>Don Mills North</td>
    </tr>
    <tr>
      <td>4</td>
      <td>M6B</td>
      <td>North York</td>
      <td>Glencairn</td>
    </tr>
  </tbody>
</table>
</div>



### At the end final_df.shape is as follows¶


```python
final_df.shape
```




    (103, 3)



## Now that we have built a dataframe of the postal code of each neighborhood along with the borough name and neighborhood name, in order to utilize the Foursquare location data, we need to get the latitude and the longitude coordinates of each neighborhood.

## Question 2: Add to the final table the longitude and latitude for each postal code

### Loading the excel with the longitude and latitude information


```python
df_data_1 = pd.read_csv('Geospatial_Coordinates.csv')
df_data_1.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Postal Code</th>
      <th>Latitude</th>
      <th>Longitude</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>M1B</td>
      <td>43.806686</td>
      <td>-79.194353</td>
    </tr>
    <tr>
      <td>1</td>
      <td>M1C</td>
      <td>43.784535</td>
      <td>-79.160497</td>
    </tr>
    <tr>
      <td>2</td>
      <td>M1E</td>
      <td>43.763573</td>
      <td>-79.188711</td>
    </tr>
    <tr>
      <td>3</td>
      <td>M1G</td>
      <td>43.770992</td>
      <td>-79.216917</td>
    </tr>
    <tr>
      <td>4</td>
      <td>M1H</td>
      <td>43.773136</td>
      <td>-79.239476</td>
    </tr>
  </tbody>
</table>
</div>



### It is important to note that the information for the length and latitude excel is already sorted in the Postal Code column

### To be able to join both dataframe correctly it is necessary to order our final_df


```python
# These lines of code order the dataframe, reset the index and add the longitude and latitude information
dforden = final_df.sort_values(['PostalCode'])
M = final_df.shape[0]
Q = list(range(0,M))

dforden[''] = Q
dforden.set_index('', inplace=True)

Latitude1 = df_data_1['Latitude'].tolist()
Longitude1 = df_data_1['Longitude'].tolist()

dforden['Latitude'] = Latitude1
dforden['Longitude'] = Longitude1
```

### In the end our dataframe dforden with the longitude and latitude information is as follows:


```python
dforden.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>PostalCode</th>
      <th>Borough</th>
      <th>Neighborhood</th>
      <th>Latitude</th>
      <th>Longitude</th>
    </tr>
    <tr>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>M1B</td>
      <td>Scarborough</td>
      <td>Rouge, Malvern</td>
      <td>43.806686</td>
      <td>-79.194353</td>
    </tr>
    <tr>
      <td>1</td>
      <td>M1C</td>
      <td>Scarborough</td>
      <td>Highland Creek, Rouge Hill, Port Union</td>
      <td>43.784535</td>
      <td>-79.160497</td>
    </tr>
    <tr>
      <td>2</td>
      <td>M1E</td>
      <td>Scarborough</td>
      <td>Guildwood, Morningside, West Hill</td>
      <td>43.763573</td>
      <td>-79.188711</td>
    </tr>
    <tr>
      <td>3</td>
      <td>M1G</td>
      <td>Scarborough</td>
      <td>Woburn</td>
      <td>43.770992</td>
      <td>-79.216917</td>
    </tr>
    <tr>
      <td>4</td>
      <td>M1H</td>
      <td>Scarborough</td>
      <td>Cedarbrae</td>
      <td>43.773136</td>
      <td>-79.239476</td>
    </tr>
  </tbody>
</table>
</div>



## Question 3: Replicate the analysis done to neighborhoods in New York City

### Before starting the clustering process, we must filter our dataframe so that we have only Borough values that contain the word Toronto 


```python
# We will filter from the final table only the Borough values ​​that contain the word Toronto in them
df_Toronto = dforden[dforden['Borough'].str.contains("Toronto", case=True)].reset_index()

#Fixing the last details in the Toronto dataframe
M1 = df_Toronto.shape[0]
Q1 = list(range(0,M1))
df_Toronto[''] = Q1
df_Toronto.set_index('', inplace=True)

df_Toronto.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>PostalCode</th>
      <th>Borough</th>
      <th>Neighborhood</th>
      <th>Latitude</th>
      <th>Longitude</th>
    </tr>
    <tr>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>M4E</td>
      <td>East Toronto</td>
      <td>The Beaches</td>
      <td>43.676357</td>
      <td>-79.293031</td>
    </tr>
    <tr>
      <td>1</td>
      <td>M4K</td>
      <td>East Toronto</td>
      <td>The Danforth West, Riverdale</td>
      <td>43.679557</td>
      <td>-79.352188</td>
    </tr>
    <tr>
      <td>2</td>
      <td>M4L</td>
      <td>East Toronto</td>
      <td>The Beaches West, India Bazaar</td>
      <td>43.668999</td>
      <td>-79.315572</td>
    </tr>
    <tr>
      <td>3</td>
      <td>M4M</td>
      <td>East Toronto</td>
      <td>Studio District</td>
      <td>43.659526</td>
      <td>-79.340923</td>
    </tr>
    <tr>
      <td>4</td>
      <td>M4N</td>
      <td>Central Toronto</td>
      <td>Lawrence Park</td>
      <td>43.728020</td>
      <td>-79.388790</td>
    </tr>
  </tbody>
</table>
</div>



### Since we have the Toronto dataframe we can start replicating the clustering analysis in New York City


```python
# importing the necessary libraries
pd.set_option('display.max_columns', None)
pd.set_option('display.max_rows', None)

!conda install -c conda-forge geopy --yes 
!conda install -c conda-forge folium=0.5.0 --yes 

import numpy as np 
import json 
import requests 
import matplotlib.cm as cm
import matplotlib.colors as colors
import folium 

from sklearn.cluster import KMeans
from pandas.io.json import json_normalize 
from geopy.geocoders import Nominatim 
```

    Solving environment: done
    
    
    ==> WARNING: A newer version of conda exists. <==
      current version: 4.5.11
      latest version: 4.7.12
    
    Please update conda by running
    
        $ conda update -n base -c defaults conda
    
    
    
    # All requested packages already installed.
    
    Solving environment: done
    
    
    ==> WARNING: A newer version of conda exists. <==
      current version: 4.5.11
      latest version: 4.7.12
    
    Please update conda by running
    
        $ conda update -n base -c defaults conda
    
    
    
    # All requested packages already installed.
    
    Libraries imported.



```python
CLIENT_ID = 'XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX' # my Foursquare ID
CLIENT_SECRET = 'XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX' # my Foursquare Secret
VERSION = '20180605' # Foursquare API version
```

### Let's explore our neighborhoods in our Toronto dataframe and let's get the top 100 venues that are in our neighborhoods within a radius of 500 meters. (as we did in the analysis of new york city neighborhoods)


```python
# we know that all the information is in the items key. Before we proceed, let's borrow the get_category_type
# function that extracts the category of the venue
def get_category_type(row):
    try:
        categories_list = row['categories']
    except:
        categories_list = row['venue.categories']
        
    if len(categories_list) == 0:
        return None
    else:
        return categories_list[0]['name']

def getNearbyVenues(names, latitudes, longitudes):
    
    LIMIT = 100 # limit of number of venues returned by Foursquare API
    radius = 500 # define radius

    venues_list=[]
    for name, lat, lng in zip(names, latitudes, longitudes):
        print(name)
            
        # create the API request URL
        url = 'https://api.foursquare.com/v2/venues/explore?&client_id={}&client_secret={}&v={}&ll={},{}&radius={}&limit={}'.format(
            CLIENT_ID, 
            CLIENT_SECRET, 
            VERSION, 
            lat, 
            lng, 
            radius, 
            LIMIT)
            
        # make the GET request
        results = requests.get(url).json()["response"]['groups'][0]['items']
        
        # return only relevant information for each nearby venue
        venues_list.append([(
            name, 
            lat, 
            lng, 
            v['venue']['name'], 
            v['venue']['location']['lat'], 
            v['venue']['location']['lng'],  
            v['venue']['categories'][0]['name']) for v in results])

    nearby_venues = pd.DataFrame([item for venue_list in venues_list for item in venue_list])
    nearby_venues.columns = ['Neighborhood', 
                  'Neighborhood Latitude', 
                  'Neighborhood Longitude', 
                  'Venue', 
                  'Venue Latitude', 
                  'Venue Longitude', 
                  'Venue Category']
    
    return(nearby_venues)
```


```python
Toronto_venues = getNearbyVenues(names = df_Toronto['Neighborhood'],
                                   latitudes = df_Toronto['Latitude'],
                                   longitudes = df_Toronto['Longitude'])
```

    The Beaches
    The Danforth West, Riverdale
    The Beaches West, India Bazaar
    Studio District
    Lawrence Park
    Davisville North
    North Toronto West
    Davisville
    Moore Park, Summerhill East
    Deer Park, Forest Hill SE, Rathnelly, South Hill, Summerhill West
    Rosedale
    Cabbagetown, St. James Town
    Church and Wellesley
    Harbourfront, Regent Park
    Ryerson, Garden District
    St. James Town
    Berczy Park
    Central Bay Street
    Adelaide, King, Richmond
    Harbourfront East, Toronto Islands, Union Station
    Design Exchange, Toronto Dominion Centre
    Commerce Court, Victoria Hotel
    Roselawn
    Forest Hill North, Forest Hill West
    The Annex, North Midtown, Yorkville
    Harbord, University of Toronto
    Chinatown, Grange Park, Kensington Market
    CN Tower, Bathurst Quay, Island airport, Harbourfront West, King and Spadina, Railway Lands, South Niagara
    Stn A PO Boxes 25 The Esplanade
    First Canadian Place, Underground city
    Christie
    Dovercourt Village, Dufferin
    Little Portugal, Trinity
    Brockton, Exhibition Place, Parkdale Village
    High Park, The Junction South
    Parkdale, Roncesvalles
    Runnymede, Swansea
    Business Reply Mail Processing Centre 969 Eastern



```python
Toronto_venues.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Neighborhood</th>
      <th>Neighborhood Latitude</th>
      <th>Neighborhood Longitude</th>
      <th>Venue</th>
      <th>Venue Latitude</th>
      <th>Venue Longitude</th>
      <th>Venue Category</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>The Beaches</td>
      <td>43.676357</td>
      <td>-79.293031</td>
      <td>Glen Manor Ravine</td>
      <td>43.676821</td>
      <td>-79.293942</td>
      <td>Trail</td>
    </tr>
    <tr>
      <td>1</td>
      <td>The Beaches</td>
      <td>43.676357</td>
      <td>-79.293031</td>
      <td>The Big Carrot Natural Food Market</td>
      <td>43.678879</td>
      <td>-79.297734</td>
      <td>Health Food Store</td>
    </tr>
    <tr>
      <td>2</td>
      <td>The Beaches</td>
      <td>43.676357</td>
      <td>-79.293031</td>
      <td>Grover Pub and Grub</td>
      <td>43.679181</td>
      <td>-79.297215</td>
      <td>Pub</td>
    </tr>
    <tr>
      <td>3</td>
      <td>The Beaches</td>
      <td>43.676357</td>
      <td>-79.293031</td>
      <td>Upper Beaches</td>
      <td>43.680563</td>
      <td>-79.292869</td>
      <td>Neighborhood</td>
    </tr>
    <tr>
      <td>4</td>
      <td>The Danforth West, Riverdale</td>
      <td>43.679557</td>
      <td>-79.352188</td>
      <td>Pantheon</td>
      <td>43.677621</td>
      <td>-79.351434</td>
      <td>Greek Restaurant</td>
    </tr>
  </tbody>
</table>
</div>



### It's time to analyze each Neighborhood and group rows by Neighborhood and by taking the mean of the frequency of occurrence of each category


```python
# one hot encoding
Toronto_onehot = pd.get_dummies(Toronto_venues[['Venue Category']], prefix="", prefix_sep="")

# add neighborhood column back to dataframe
Toronto_onehot['Neighborhood'] = Toronto_venues['Neighborhood'] 

# move neighborhood column to the first column
fixed_columns = [Toronto_onehot.columns[-1]] + list(Toronto_onehot.columns[:-1])
Toronto_onehot = Toronto_onehot[fixed_columns]
```


```python
Toronto_grouped = Toronto_onehot.groupby('Neighborhood').mean().reset_index()
Toronto_grouped.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Neighborhood</th>
      <th>Yoga Studio</th>
      <th>Afghan Restaurant</th>
      <th>Airport</th>
      <th>Airport Food Court</th>
      <th>Airport Gate</th>
      <th>Airport Lounge</th>
      <th>Airport Service</th>
      <th>Airport Terminal</th>
      <th>American Restaurant</th>
      <th>Antique Shop</th>
      <th>Aquarium</th>
      <th>Art Gallery</th>
      <th>Art Museum</th>
      <th>Arts &amp; Crafts Store</th>
      <th>Asian Restaurant</th>
      <th>Athletics &amp; Sports</th>
      <th>Auto Workshop</th>
      <th>BBQ Joint</th>
      <th>Baby Store</th>
      <th>Bagel Shop</th>
      <th>Bakery</th>
      <th>Bank</th>
      <th>Bar</th>
      <th>Baseball Stadium</th>
      <th>Basketball Stadium</th>
      <th>Beach</th>
      <th>Bed &amp; Breakfast</th>
      <th>Beer Bar</th>
      <th>Beer Store</th>
      <th>Belgian Restaurant</th>
      <th>Bistro</th>
      <th>Boat or Ferry</th>
      <th>Bookstore</th>
      <th>Boutique</th>
      <th>Brazilian Restaurant</th>
      <th>Breakfast Spot</th>
      <th>Brewery</th>
      <th>Bubble Tea Shop</th>
      <th>Building</th>
      <th>Burger Joint</th>
      <th>Burrito Place</th>
      <th>Bus Line</th>
      <th>Butcher</th>
      <th>Café</th>
      <th>Cajun / Creole Restaurant</th>
      <th>Camera Store</th>
      <th>Caribbean Restaurant</th>
      <th>Cheese Shop</th>
      <th>Chinese Restaurant</th>
      <th>Chocolate Shop</th>
      <th>Church</th>
      <th>Climbing Gym</th>
      <th>Clothing Store</th>
      <th>Cocktail Bar</th>
      <th>Coffee Shop</th>
      <th>College Arts Building</th>
      <th>College Gym</th>
      <th>College Rec Center</th>
      <th>Colombian Restaurant</th>
      <th>Comfort Food Restaurant</th>
      <th>Comic Shop</th>
      <th>Concert Hall</th>
      <th>Convenience Store</th>
      <th>Cosmetics Shop</th>
      <th>Coworking Space</th>
      <th>Creperie</th>
      <th>Cuban Restaurant</th>
      <th>Cupcake Shop</th>
      <th>Dance Studio</th>
      <th>Deli / Bodega</th>
      <th>Department Store</th>
      <th>Dessert Shop</th>
      <th>Dim Sum Restaurant</th>
      <th>Diner</th>
      <th>Discount Store</th>
      <th>Dog Run</th>
      <th>Doner Restaurant</th>
      <th>Donut Shop</th>
      <th>Dumpling Restaurant</th>
      <th>Eastern European Restaurant</th>
      <th>Electronics Store</th>
      <th>Ethiopian Restaurant</th>
      <th>Event Space</th>
      <th>Falafel Restaurant</th>
      <th>Farmers Market</th>
      <th>Fast Food Restaurant</th>
      <th>Festival</th>
      <th>Filipino Restaurant</th>
      <th>Fish &amp; Chips Shop</th>
      <th>Fish Market</th>
      <th>Flea Market</th>
      <th>Flower Shop</th>
      <th>Food</th>
      <th>Food &amp; Drink Shop</th>
      <th>Food Court</th>
      <th>Food Truck</th>
      <th>Fountain</th>
      <th>French Restaurant</th>
      <th>Fried Chicken Joint</th>
      <th>Fruit &amp; Vegetable Store</th>
      <th>Furniture / Home Store</th>
      <th>Gaming Cafe</th>
      <th>Garden</th>
      <th>Garden Center</th>
      <th>Gastropub</th>
      <th>Gay Bar</th>
      <th>General Entertainment</th>
      <th>General Travel</th>
      <th>Gift Shop</th>
      <th>Gluten-free Restaurant</th>
      <th>Gourmet Shop</th>
      <th>Greek Restaurant</th>
      <th>Grocery Store</th>
      <th>Gym</th>
      <th>Gym / Fitness Center</th>
      <th>Harbor / Marina</th>
      <th>Health &amp; Beauty Service</th>
      <th>Health Food Store</th>
      <th>Historic Site</th>
      <th>History Museum</th>
      <th>Hobby Shop</th>
      <th>Home Service</th>
      <th>Hookah Bar</th>
      <th>Hospital</th>
      <th>Hostel</th>
      <th>Hotel</th>
      <th>Hotel Bar</th>
      <th>Hotpot Restaurant</th>
      <th>Ice Cream Shop</th>
      <th>Indian Restaurant</th>
      <th>Indie Movie Theater</th>
      <th>Indoor Play Area</th>
      <th>Intersection</th>
      <th>Irish Pub</th>
      <th>Italian Restaurant</th>
      <th>Japanese Restaurant</th>
      <th>Jazz Club</th>
      <th>Jewelry Store</th>
      <th>Jewish Restaurant</th>
      <th>Juice Bar</th>
      <th>Korean Restaurant</th>
      <th>Lake</th>
      <th>Latin American Restaurant</th>
      <th>Light Rail Station</th>
      <th>Lingerie Store</th>
      <th>Liquor Store</th>
      <th>Lounge</th>
      <th>Mac &amp; Cheese Joint</th>
      <th>Malay Restaurant</th>
      <th>Market</th>
      <th>Martial Arts Dojo</th>
      <th>Mediterranean Restaurant</th>
      <th>Men's Store</th>
      <th>Metro Station</th>
      <th>Mexican Restaurant</th>
      <th>Middle Eastern Restaurant</th>
      <th>Miscellaneous Shop</th>
      <th>Modern European Restaurant</th>
      <th>Molecular Gastronomy Restaurant</th>
      <th>Monument / Landmark</th>
      <th>Movie Theater</th>
      <th>Museum</th>
      <th>Music Venue</th>
      <th>New American Restaurant</th>
      <th>Nightclub</th>
      <th>Noodle House</th>
      <th>Office</th>
      <th>Opera House</th>
      <th>Optical Shop</th>
      <th>Organic Grocery</th>
      <th>Other Great Outdoors</th>
      <th>Outdoor Sculpture</th>
      <th>Park</th>
      <th>Performing Arts Venue</th>
      <th>Persian Restaurant</th>
      <th>Pet Store</th>
      <th>Pharmacy</th>
      <th>Pizza Place</th>
      <th>Plane</th>
      <th>Playground</th>
      <th>Plaza</th>
      <th>Poke Place</th>
      <th>Polish Restaurant</th>
      <th>Pool</th>
      <th>Portuguese Restaurant</th>
      <th>Post Office</th>
      <th>Poutine Place</th>
      <th>Pub</th>
      <th>Ramen Restaurant</th>
      <th>Record Shop</th>
      <th>Rental Car Location</th>
      <th>Restaurant</th>
      <th>Roof Deck</th>
      <th>Sake Bar</th>
      <th>Salad Place</th>
      <th>Salon / Barbershop</th>
      <th>Sandwich Place</th>
      <th>Scenic Lookout</th>
      <th>Sculpture Garden</th>
      <th>Seafood Restaurant</th>
      <th>Shoe Store</th>
      <th>Shopping Mall</th>
      <th>Skate Park</th>
      <th>Skating Rink</th>
      <th>Smoke Shop</th>
      <th>Smoothie Shop</th>
      <th>Snack Place</th>
      <th>Soup Place</th>
      <th>Southern / Soul Food Restaurant</th>
      <th>Spa</th>
      <th>Speakeasy</th>
      <th>Sporting Goods Shop</th>
      <th>Sports Bar</th>
      <th>Stadium</th>
      <th>Stationery Store</th>
      <th>Steakhouse</th>
      <th>Strip Club</th>
      <th>Supermarket</th>
      <th>Sushi Restaurant</th>
      <th>Swim School</th>
      <th>Taco Place</th>
      <th>Tailor Shop</th>
      <th>Taiwanese Restaurant</th>
      <th>Tanning Salon</th>
      <th>Tapas Restaurant</th>
      <th>Tea Room</th>
      <th>Tennis Court</th>
      <th>Thai Restaurant</th>
      <th>Theater</th>
      <th>Theme Restaurant</th>
      <th>Toy / Game Store</th>
      <th>Trail</th>
      <th>Train Station</th>
      <th>Vegetarian / Vegan Restaurant</th>
      <th>Video Game Store</th>
      <th>Vietnamese Restaurant</th>
      <th>Wine Bar</th>
      <th>Wings Joint</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>Adelaide, King, Richmond</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0000</td>
      <td>0.0000</td>
      <td>0.0000</td>
      <td>0.000</td>
      <td>0.000</td>
      <td>0.000</td>
      <td>0.03</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.010000</td>
      <td>0.01</td>
      <td>0.0</td>
      <td>0.02</td>
      <td>0.0</td>
      <td>0.0000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.020000</td>
      <td>0.0</td>
      <td>0.040000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0000</td>
      <td>0.01</td>
      <td>0.0000</td>
      <td>0.01</td>
      <td>0.020000</td>
      <td>0.0000</td>
      <td>0.0</td>
      <td>0.01</td>
      <td>0.03</td>
      <td>0.010000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.050000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.010000</td>
      <td>0.000000</td>
      <td>0.080000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.01</td>
      <td>0.000000</td>
      <td>0.0000</td>
      <td>0.020000</td>
      <td>0.000000</td>
      <td>0.03</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.01</td>
      <td>0.01</td>
      <td>0.01</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.01</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.01</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0000</td>
      <td>0.0000</td>
      <td>0.02</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.01</td>
      <td>0.0</td>
      <td>0.01</td>
      <td>0.000000</td>
      <td>0.010000</td>
      <td>0.000000</td>
      <td>0.020000</td>
      <td>0.01</td>
      <td>0.0000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.030000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.01</td>
      <td>0.01</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.010000</td>
      <td>0.01</td>
      <td>0.010000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.01</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.01</td>
      <td>0.000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.020000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.01</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.01</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.01</td>
      <td>0.0</td>
      <td>0.01</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0200</td>
      <td>0.0000</td>
      <td>0.0</td>
      <td>0.01</td>
      <td>0.01</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.01</td>
      <td>0.0</td>
      <td>0.030000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.02</td>
      <td>0.01</td>
      <td>0.01</td>
      <td>0.0</td>
      <td>0.0000</td>
      <td>0.010000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0000</td>
      <td>0.0</td>
      <td>0.0100</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0000</td>
      <td>0.01</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.040000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.02</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.030000</td>
      <td>0.01</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.020000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.01</td>
      <td>0.0</td>
    </tr>
    <tr>
      <td>1</td>
      <td>Berczy Park</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0000</td>
      <td>0.0000</td>
      <td>0.0000</td>
      <td>0.000</td>
      <td>0.000</td>
      <td>0.000</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.018182</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0000</td>
      <td>0.018182</td>
      <td>0.0</td>
      <td>0.018182</td>
      <td>0.036364</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.018182</td>
      <td>0.018182</td>
      <td>0.0</td>
      <td>0.036364</td>
      <td>0.0</td>
      <td>0.018182</td>
      <td>0.018182</td>
      <td>0.0000</td>
      <td>0.00</td>
      <td>0.0000</td>
      <td>0.00</td>
      <td>0.018182</td>
      <td>0.0000</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.036364</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.036364</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.018182</td>
      <td>0.054545</td>
      <td>0.072727</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.018182</td>
      <td>0.0000</td>
      <td>0.018182</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.018182</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.018182</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.018182</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.036364</td>
      <td>0.0000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.018182</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.018182</td>
      <td>0.018182</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0000</td>
      <td>0.0000</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.018182</td>
      <td>0.018182</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.0000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.018182</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.018182</td>
      <td>0.018182</td>
      <td>0.00</td>
      <td>0.018182</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.000</td>
      <td>0.0</td>
      <td>0.018182</td>
      <td>0.018182</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.018182</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.018182</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0000</td>
      <td>0.0000</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.018182</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.018182</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0000</td>
      <td>0.036364</td>
      <td>0.0</td>
      <td>0.018182</td>
      <td>0.0000</td>
      <td>0.0</td>
      <td>0.0000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0000</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.036364</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.018182</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.018182</td>
      <td>0.0</td>
      <td>0.018182</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.018182</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.0</td>
    </tr>
    <tr>
      <td>2</td>
      <td>Brockton, Exhibition Place, Parkdale Village</td>
      <td>0.045455</td>
      <td>0.0</td>
      <td>0.0000</td>
      <td>0.0000</td>
      <td>0.0000</td>
      <td>0.000</td>
      <td>0.000</td>
      <td>0.000</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.045455</td>
      <td>0.0</td>
      <td>0.045455</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0000</td>
      <td>0.00</td>
      <td>0.0000</td>
      <td>0.00</td>
      <td>0.090909</td>
      <td>0.0000</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0.045455</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.090909</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.045455</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.045455</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.090909</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.000000</td>
      <td>0.0000</td>
      <td>0.000000</td>
      <td>0.045455</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.045455</td>
      <td>0.0</td>
      <td>0.0000</td>
      <td>0.0000</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.045455</td>
      <td>0.045455</td>
      <td>0.00</td>
      <td>0.0000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.045455</td>
      <td>0.000000</td>
      <td>0.045455</td>
      <td>0.00</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.045455</td>
      <td>0.0</td>
      <td>0.045455</td>
      <td>0.0</td>
      <td>0.0000</td>
      <td>0.0000</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.045455</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0000</td>
      <td>0.0</td>
      <td>0.0000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0000</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.045455</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.0</td>
    </tr>
    <tr>
      <td>3</td>
      <td>Business Reply Mail Processing Centre 969 Eastern</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0000</td>
      <td>0.0000</td>
      <td>0.0000</td>
      <td>0.000</td>
      <td>0.000</td>
      <td>0.000</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0625</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0000</td>
      <td>0.00</td>
      <td>0.0000</td>
      <td>0.00</td>
      <td>0.000000</td>
      <td>0.0625</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0.062500</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.000000</td>
      <td>0.0625</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.062500</td>
      <td>0.0625</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0625</td>
      <td>0.0625</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.0000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.125</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.062500</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0625</td>
      <td>0.0000</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.062500</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0625</td>
      <td>0.0</td>
      <td>0.0625</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0625</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.0</td>
    </tr>
    <tr>
      <td>4</td>
      <td>CN Tower, Bathurst Quay, Island airport, Harbo...</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0625</td>
      <td>0.0625</td>
      <td>0.0625</td>
      <td>0.125</td>
      <td>0.125</td>
      <td>0.125</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.062500</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0625</td>
      <td>0.00</td>
      <td>0.0625</td>
      <td>0.00</td>
      <td>0.000000</td>
      <td>0.0000</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.062500</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.000000</td>
      <td>0.0000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0000</td>
      <td>0.0000</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.0625</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0000</td>
      <td>0.0625</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0625</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0000</td>
      <td>0.0</td>
      <td>0.0000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0000</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.0</td>
    </tr>
  </tbody>
</table>
</div>



### Now let's create the new dataframe and display the top 10 venues for each neighborhood.


```python
def return_most_common_venues(row, num_top_venues):
    row_categories = row.iloc[1:]
    row_categories_sorted = row_categories.sort_values(ascending=False)
    
    return row_categories_sorted.index.values[0:num_top_venues]

num_top_venues = 10

indicators = ['st', 'nd', 'rd']

# create columns according to number of top venues
columns = ['Neighborhood']
for ind in np.arange(num_top_venues):
    try:
        columns.append('{}{} Most Common Venue'.format(ind+1, indicators[ind]))
    except:
        columns.append('{}th Most Common Venue'.format(ind+1))

# create a new dataframe
neighborhoods_venues_sorted = pd.DataFrame(columns=columns)
neighborhoods_venues_sorted['Neighborhood'] = Toronto_grouped['Neighborhood']

for ind in np.arange(Toronto_grouped.shape[0]):
    neighborhoods_venues_sorted.iloc[ind, 1:] = return_most_common_venues(Toronto_grouped.iloc[ind, :], num_top_venues)

neighborhoods_venues_sorted.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Neighborhood</th>
      <th>1st Most Common Venue</th>
      <th>2nd Most Common Venue</th>
      <th>3rd Most Common Venue</th>
      <th>4th Most Common Venue</th>
      <th>5th Most Common Venue</th>
      <th>6th Most Common Venue</th>
      <th>7th Most Common Venue</th>
      <th>8th Most Common Venue</th>
      <th>9th Most Common Venue</th>
      <th>10th Most Common Venue</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>Adelaide, King, Richmond</td>
      <td>Coffee Shop</td>
      <td>Café</td>
      <td>Steakhouse</td>
      <td>Bar</td>
      <td>Thai Restaurant</td>
      <td>Hotel</td>
      <td>Burger Joint</td>
      <td>American Restaurant</td>
      <td>Restaurant</td>
      <td>Cosmetics Shop</td>
    </tr>
    <tr>
      <td>1</td>
      <td>Berczy Park</td>
      <td>Coffee Shop</td>
      <td>Cocktail Bar</td>
      <td>Bakery</td>
      <td>Steakhouse</td>
      <td>Cheese Shop</td>
      <td>Farmers Market</td>
      <td>Café</td>
      <td>Beer Bar</td>
      <td>Seafood Restaurant</td>
      <td>Comfort Food Restaurant</td>
    </tr>
    <tr>
      <td>2</td>
      <td>Brockton, Exhibition Place, Parkdale Village</td>
      <td>Breakfast Spot</td>
      <td>Café</td>
      <td>Coffee Shop</td>
      <td>Furniture / Home Store</td>
      <td>Burrito Place</td>
      <td>Convenience Store</td>
      <td>Restaurant</td>
      <td>Caribbean Restaurant</td>
      <td>Stadium</td>
      <td>Bar</td>
    </tr>
    <tr>
      <td>3</td>
      <td>Business Reply Mail Processing Centre 969 Eastern</td>
      <td>Light Rail Station</td>
      <td>Fast Food Restaurant</td>
      <td>Burrito Place</td>
      <td>Auto Workshop</td>
      <td>Spa</td>
      <td>Garden</td>
      <td>Garden Center</td>
      <td>Brewery</td>
      <td>Park</td>
      <td>Smoke Shop</td>
    </tr>
    <tr>
      <td>4</td>
      <td>CN Tower, Bathurst Quay, Island airport, Harbo...</td>
      <td>Airport Terminal</td>
      <td>Airport Lounge</td>
      <td>Airport Service</td>
      <td>Sculpture Garden</td>
      <td>Coffee Shop</td>
      <td>Plane</td>
      <td>Boat or Ferry</td>
      <td>Boutique</td>
      <td>Bar</td>
      <td>Airport Gate</td>
    </tr>
  </tbody>
</table>
</div>



### With the neighborhoods_venues_sorted dataframe we can start our clustering analysis as it shows us the 10 most common venue in each neighborhood

### It's time to start making clusters


```python
# add clustering labels
neighborhoods_venues_sorted.insert(0, 'Cluster Labels', kmeans.labels_)

Toronto_merged = df_Toronto

# merge toronto_grouped with toronto_data to add latitude/longitude for each neighborhood
Toronto_merged = Toronto_merged.join(neighborhoods_venues_sorted.set_index('Neighborhood'), on='Neighborhood')

Toronto_merged.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>PostalCode</th>
      <th>Borough</th>
      <th>Neighborhood</th>
      <th>Latitude</th>
      <th>Longitude</th>
      <th>Cluster Labels</th>
      <th>1st Most Common Venue</th>
      <th>2nd Most Common Venue</th>
      <th>3rd Most Common Venue</th>
      <th>4th Most Common Venue</th>
      <th>5th Most Common Venue</th>
      <th>6th Most Common Venue</th>
      <th>7th Most Common Venue</th>
      <th>8th Most Common Venue</th>
      <th>9th Most Common Venue</th>
      <th>10th Most Common Venue</th>
    </tr>
    <tr>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>M4E</td>
      <td>East Toronto</td>
      <td>The Beaches</td>
      <td>43.676357</td>
      <td>-79.293031</td>
      <td>0</td>
      <td>Health Food Store</td>
      <td>Trail</td>
      <td>Pub</td>
      <td>Diner</td>
      <td>Fast Food Restaurant</td>
      <td>Farmers Market</td>
      <td>Falafel Restaurant</td>
      <td>Event Space</td>
      <td>Ethiopian Restaurant</td>
      <td>Electronics Store</td>
    </tr>
    <tr>
      <td>1</td>
      <td>M4K</td>
      <td>East Toronto</td>
      <td>The Danforth West, Riverdale</td>
      <td>43.679557</td>
      <td>-79.352188</td>
      <td>0</td>
      <td>Greek Restaurant</td>
      <td>Coffee Shop</td>
      <td>Italian Restaurant</td>
      <td>Ice Cream Shop</td>
      <td>Furniture / Home Store</td>
      <td>Bookstore</td>
      <td>Pub</td>
      <td>Indian Restaurant</td>
      <td>Sports Bar</td>
      <td>Spa</td>
    </tr>
    <tr>
      <td>2</td>
      <td>M4L</td>
      <td>East Toronto</td>
      <td>The Beaches West, India Bazaar</td>
      <td>43.668999</td>
      <td>-79.315572</td>
      <td>0</td>
      <td>Pet Store</td>
      <td>Park</td>
      <td>Brewery</td>
      <td>Sandwich Place</td>
      <td>Burger Joint</td>
      <td>Burrito Place</td>
      <td>Pub</td>
      <td>Pizza Place</td>
      <td>Movie Theater</td>
      <td>Sushi Restaurant</td>
    </tr>
    <tr>
      <td>3</td>
      <td>M4M</td>
      <td>East Toronto</td>
      <td>Studio District</td>
      <td>43.659526</td>
      <td>-79.340923</td>
      <td>0</td>
      <td>Café</td>
      <td>Coffee Shop</td>
      <td>Bakery</td>
      <td>Italian Restaurant</td>
      <td>American Restaurant</td>
      <td>Yoga Studio</td>
      <td>Comfort Food Restaurant</td>
      <td>Seafood Restaurant</td>
      <td>Brewery</td>
      <td>Sandwich Place</td>
    </tr>
    <tr>
      <td>4</td>
      <td>M4N</td>
      <td>Central Toronto</td>
      <td>Lawrence Park</td>
      <td>43.728020</td>
      <td>-79.388790</td>
      <td>3</td>
      <td>Park</td>
      <td>Bus Line</td>
      <td>Swim School</td>
      <td>Wings Joint</td>
      <td>Discount Store</td>
      <td>Fast Food Restaurant</td>
      <td>Farmers Market</td>
      <td>Falafel Restaurant</td>
      <td>Event Space</td>
      <td>Ethiopian Restaurant</td>
    </tr>
  </tbody>
</table>
</div>



### Since each neighborhood was assigned a Cluster Label we can create our map and thus see better what is really happening


```python
# set number of clusters
kclusters = 5

Toronto_grouped_clustering = Toronto_grouped.drop('Neighborhood', 1)

# run k-means clustering
kmeans = KMeans(n_clusters=kclusters, random_state=0).fit(Toronto_grouped_clustering)

# create map
address = 'Toronto, Ont'

geolocator = Nominatim(user_agent="ny_explorer")
location = geolocator.geocode(address)
latitude = location.latitude
longitude = location.longitude
map_clusters = folium.Map(location=[latitude, longitude], zoom_start=11)

# set color scheme for the clusters
x = np.arange(kclusters)
ys = [i + x + (i*x)**2 for i in range(kclusters)]
colors_array = cm.rainbow(np.linspace(0, 1, len(ys)))
rainbow = [colors.rgb2hex(i) for i in colors_array]

# add markers to the map
markers_colors = []
for lat, lon, poi, cluster in zip(Toronto_merged['Latitude'], Toronto_merged['Longitude'], Toronto_merged['Neighborhood'], Toronto_merged['Cluster Labels']):
    label = folium.Popup(str(poi) + ' Cluster ' + str(cluster), parse_html=True)
    folium.CircleMarker(
        [lat, lon],
        radius=5,
        popup=label,
        color=rainbow[cluster-1],
        fill=True,
        fill_color=rainbow[cluster-1],
        fill_opacity=0.7).add_to(map_clusters)
       
map_clusters
```




<div style="width:100%;"><div style="position:relative;width:100%;height:0;padding-bottom:60%;"><iframe src="data:text/html;charset=utf-8;base64,PCFET0NUWVBFIGh0bWw+CjxoZWFkPiAgICAKICAgIDxtZXRhIGh0dHAtZXF1aXY9ImNvbnRlbnQtdHlwZSIgY29udGVudD0idGV4dC9odG1sOyBjaGFyc2V0PVVURi04IiAvPgogICAgPHNjcmlwdD5MX1BSRUZFUl9DQU5WQVMgPSBmYWxzZTsgTF9OT19UT1VDSCA9IGZhbHNlOyBMX0RJU0FCTEVfM0QgPSBmYWxzZTs8L3NjcmlwdD4KICAgIDxzY3JpcHQgc3JjPSJodHRwczovL2Nkbi5qc2RlbGl2ci5uZXQvbnBtL2xlYWZsZXRAMS4yLjAvZGlzdC9sZWFmbGV0LmpzIj48L3NjcmlwdD4KICAgIDxzY3JpcHQgc3JjPSJodHRwczovL2FqYXguZ29vZ2xlYXBpcy5jb20vYWpheC9saWJzL2pxdWVyeS8xLjExLjEvanF1ZXJ5Lm1pbi5qcyI+PC9zY3JpcHQ+CiAgICA8c2NyaXB0IHNyYz0iaHR0cHM6Ly9tYXhjZG4uYm9vdHN0cmFwY2RuLmNvbS9ib290c3RyYXAvMy4yLjAvanMvYm9vdHN0cmFwLm1pbi5qcyI+PC9zY3JpcHQ+CiAgICA8c2NyaXB0IHNyYz0iaHR0cHM6Ly9jZG5qcy5jbG91ZGZsYXJlLmNvbS9hamF4L2xpYnMvTGVhZmxldC5hd2Vzb21lLW1hcmtlcnMvMi4wLjIvbGVhZmxldC5hd2Vzb21lLW1hcmtlcnMuanMiPjwvc2NyaXB0PgogICAgPGxpbmsgcmVsPSJzdHlsZXNoZWV0IiBocmVmPSJodHRwczovL2Nkbi5qc2RlbGl2ci5uZXQvbnBtL2xlYWZsZXRAMS4yLjAvZGlzdC9sZWFmbGV0LmNzcyIvPgogICAgPGxpbmsgcmVsPSJzdHlsZXNoZWV0IiBocmVmPSJodHRwczovL21heGNkbi5ib290c3RyYXBjZG4uY29tL2Jvb3RzdHJhcC8zLjIuMC9jc3MvYm9vdHN0cmFwLm1pbi5jc3MiLz4KICAgIDxsaW5rIHJlbD0ic3R5bGVzaGVldCIgaHJlZj0iaHR0cHM6Ly9tYXhjZG4uYm9vdHN0cmFwY2RuLmNvbS9ib290c3RyYXAvMy4yLjAvY3NzL2Jvb3RzdHJhcC10aGVtZS5taW4uY3NzIi8+CiAgICA8bGluayByZWw9InN0eWxlc2hlZXQiIGhyZWY9Imh0dHBzOi8vbWF4Y2RuLmJvb3RzdHJhcGNkbi5jb20vZm9udC1hd2Vzb21lLzQuNi4zL2Nzcy9mb250LWF3ZXNvbWUubWluLmNzcyIvPgogICAgPGxpbmsgcmVsPSJzdHlsZXNoZWV0IiBocmVmPSJodHRwczovL2NkbmpzLmNsb3VkZmxhcmUuY29tL2FqYXgvbGlicy9MZWFmbGV0LmF3ZXNvbWUtbWFya2Vycy8yLjAuMi9sZWFmbGV0LmF3ZXNvbWUtbWFya2Vycy5jc3MiLz4KICAgIDxsaW5rIHJlbD0ic3R5bGVzaGVldCIgaHJlZj0iaHR0cHM6Ly9yYXdnaXQuY29tL3B5dGhvbi12aXN1YWxpemF0aW9uL2ZvbGl1bS9tYXN0ZXIvZm9saXVtL3RlbXBsYXRlcy9sZWFmbGV0LmF3ZXNvbWUucm90YXRlLmNzcyIvPgogICAgPHN0eWxlPmh0bWwsIGJvZHkge3dpZHRoOiAxMDAlO2hlaWdodDogMTAwJTttYXJnaW46IDA7cGFkZGluZzogMDt9PC9zdHlsZT4KICAgIDxzdHlsZT4jbWFwIHtwb3NpdGlvbjphYnNvbHV0ZTt0b3A6MDtib3R0b206MDtyaWdodDowO2xlZnQ6MDt9PC9zdHlsZT4KICAgIAogICAgICAgICAgICA8c3R5bGU+ICNtYXBfMzhkZTM4NzE2ZGE2NGUzMzg3MzQ1NDA3YTAwOWU1NjMgewogICAgICAgICAgICAgICAgcG9zaXRpb24gOiByZWxhdGl2ZTsKICAgICAgICAgICAgICAgIHdpZHRoIDogMTAwLjAlOwogICAgICAgICAgICAgICAgaGVpZ2h0OiAxMDAuMCU7CiAgICAgICAgICAgICAgICBsZWZ0OiAwLjAlOwogICAgICAgICAgICAgICAgdG9wOiAwLjAlOwogICAgICAgICAgICAgICAgfQogICAgICAgICAgICA8L3N0eWxlPgogICAgICAgIAo8L2hlYWQ+Cjxib2R5PiAgICAKICAgIAogICAgICAgICAgICA8ZGl2IGNsYXNzPSJmb2xpdW0tbWFwIiBpZD0ibWFwXzM4ZGUzODcxNmRhNjRlMzM4NzM0NTQwN2EwMDllNTYzIiA+PC9kaXY+CiAgICAgICAgCjwvYm9keT4KPHNjcmlwdD4gICAgCiAgICAKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGJvdW5kcyA9IG51bGw7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgdmFyIG1hcF8zOGRlMzg3MTZkYTY0ZTMzODczNDU0MDdhMDA5ZTU2MyA9IEwubWFwKAogICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgJ21hcF8zOGRlMzg3MTZkYTY0ZTMzODczNDU0MDdhMDA5ZTU2MycsCiAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICB7Y2VudGVyOiBbNDMuNjc4MTI1NSwtNzkuNjMyMTIzNTMzNTAyNl0sCiAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICB6b29tOiAxMSwKICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgIG1heEJvdW5kczogYm91bmRzLAogICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgbGF5ZXJzOiBbXSwKICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgIHdvcmxkQ29weUp1bXA6IGZhbHNlLAogICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgY3JzOiBMLkNSUy5FUFNHMzg1NwogICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICB9KTsKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHRpbGVfbGF5ZXJfNTU5OTgzYjdmZmRkNGY5MjgyMTVmYWFkYThmZDA1ZGQgPSBMLnRpbGVMYXllcigKICAgICAgICAgICAgICAgICdodHRwczovL3tzfS50aWxlLm9wZW5zdHJlZXRtYXAub3JnL3t6fS97eH0ve3l9LnBuZycsCiAgICAgICAgICAgICAgICB7CiAgImF0dHJpYnV0aW9uIjogbnVsbCwKICAiZGV0ZWN0UmV0aW5hIjogZmFsc2UsCiAgIm1heFpvb20iOiAxOCwKICAibWluWm9vbSI6IDEsCiAgIm5vV3JhcCI6IGZhbHNlLAogICJzdWJkb21haW5zIjogImFiYyIKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfMzhkZTM4NzE2ZGE2NGUzMzg3MzQ1NDA3YTAwOWU1NjMpOwogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzA3OWQ1ZDVhYjk3NDRhN2RhMmE2ZWRjZDYyZWIzNmZhID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjc2MzU3Mzk5OTk5OTksLTc5LjI5MzAzMTJdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiI2ZmMDAwMCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiNmZjAwMDAiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfMzhkZTM4NzE2ZGE2NGUzMzg3MzQ1NDA3YTAwOWU1NjMpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfYmJlNWVjYzc0MmJmNDU2YTgwZDgyOTcwNjdiMTg2NWYgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMjdiNDYwNTA5OTQ2NDYxN2IzNzc5N2FkMWZkM2YyNzggPSAkKCc8ZGl2IGlkPSJodG1sXzI3YjQ2MDUwOTk0NjQ2MTdiMzc3OTdhZDFmZDNmMjc4IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5UaGUgQmVhY2hlcyBDbHVzdGVyIDA8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2JiZTVlY2M3NDJiZjQ1NmE4MGQ4Mjk3MDY3YjE4NjVmLnNldENvbnRlbnQoaHRtbF8yN2I0NjA1MDk5NDY0NjE3YjM3Nzk3YWQxZmQzZjI3OCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8wNzlkNWQ1YWI5NzQ0YTdkYTJhNmVkY2Q2MmViMzZmYS5iaW5kUG9wdXAocG9wdXBfYmJlNWVjYzc0MmJmNDU2YTgwZDgyOTcwNjdiMTg2NWYpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfOWQ4ZTc4OTkwYzU1NDQ1ODg4NTA1ODM5ZmZmNDY4MGYgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42Nzk1NTcxLC03OS4zNTIxODhdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiI2ZmMDAwMCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiNmZjAwMDAiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfMzhkZTM4NzE2ZGE2NGUzMzg3MzQ1NDA3YTAwOWU1NjMpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfOGU0ZjVjZjg3YTBhNDg0YmFhNmY4ODljODRlMzg1ODAgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfZWVkNTUwODUzOWEyNGI3NDljNDI1YzVkYjM3MWU3NTUgPSAkKCc8ZGl2IGlkPSJodG1sX2VlZDU1MDg1MzlhMjRiNzQ5YzQyNWM1ZGIzNzFlNzU1IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5UaGUgRGFuZm9ydGggV2VzdCwgUml2ZXJkYWxlIENsdXN0ZXIgMDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfOGU0ZjVjZjg3YTBhNDg0YmFhNmY4ODljODRlMzg1ODAuc2V0Q29udGVudChodG1sX2VlZDU1MDg1MzlhMjRiNzQ5YzQyNWM1ZGIzNzFlNzU1KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzlkOGU3ODk5MGM1NTQ0NTg4ODUwNTgzOWZmZjQ2ODBmLmJpbmRQb3B1cChwb3B1cF84ZTRmNWNmODdhMGE0ODRiYWE2Zjg4OWM4NGUzODU4MCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl82MjNmNWU2MTkxY2U0NWY0YWM0YzRmOWU2Yjg1OTExNyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY2ODk5ODUsLTc5LjMxNTU3MTU5OTk5OTk4XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogIiNmZjAwMDAiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjZmYwMDAwIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzM4ZGUzODcxNmRhNjRlMzM4NzM0NTQwN2EwMDllNTYzKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2MwOGY1OTJhYTc2OTQ0MzFiODk4YTBjNzA0OWYzNjQ5ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzVhMDE1MjcyYTRhYzQ4OWE5YTIzNzlkM2ViZDcyNWY1ID0gJCgnPGRpdiBpZD0iaHRtbF81YTAxNTI3MmE0YWM0ODlhOWEyMzc5ZDNlYmQ3MjVmNSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+VGhlIEJlYWNoZXMgV2VzdCwgSW5kaWEgQmF6YWFyIENsdXN0ZXIgMDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfYzA4ZjU5MmFhNzY5NDQzMWI4OThhMGM3MDQ5ZjM2NDkuc2V0Q29udGVudChodG1sXzVhMDE1MjcyYTRhYzQ4OWE5YTIzNzlkM2ViZDcyNWY1KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzYyM2Y1ZTYxOTFjZTQ1ZjRhYzRjNGY5ZTZiODU5MTE3LmJpbmRQb3B1cChwb3B1cF9jMDhmNTkyYWE3Njk0NDMxYjg5OGEwYzcwNDlmMzY0OSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8zY2M1ZGViNTZkY2E0ZDg1YmYxOThiZGU4OWFhOTJhOCA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY1OTUyNTUsLTc5LjM0MDkyM10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjZmYwMDAwIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiI2ZmMDAwMCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8zOGRlMzg3MTZkYTY0ZTMzODczNDU0MDdhMDA5ZTU2Myk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF81MzllODcwYzk0YmM0YmNmYTFmZTM2MjBhNWIyM2UxMyA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8yYWYzMTQ0MzIxNmU0NzcxYjRiZjBjZGI5MWU2N2IzMSA9ICQoJzxkaXYgaWQ9Imh0bWxfMmFmMzE0NDMyMTZlNDc3MWI0YmYwY2RiOTFlNjdiMzEiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlN0dWRpbyBEaXN0cmljdCBDbHVzdGVyIDA8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzUzOWU4NzBjOTRiYzRiY2ZhMWZlMzYyMGE1YjIzZTEzLnNldENvbnRlbnQoaHRtbF8yYWYzMTQ0MzIxNmU0NzcxYjRiZjBjZGI5MWU2N2IzMSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8zY2M1ZGViNTZkY2E0ZDg1YmYxOThiZGU4OWFhOTJhOC5iaW5kUG9wdXAocG9wdXBfNTM5ZTg3MGM5NGJjNGJjZmExZmUzNjIwYTViMjNlMTMpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMzIzNDQzOGVkMDY5NDUyNGI2NzRlOTZiNTAzNmEzNGIgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My43MjgwMjA1LC03OS4zODg3OTAxXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogIiM4MGZmYjQiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjODBmZmI0IiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzM4ZGUzODcxNmRhNjRlMzM4NzM0NTQwN2EwMDllNTYzKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2E4Y2I3NjhlOGMyMjQ1ZmNhNjZiMWUwZDE3MmQ3YTY0ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2I1ZjYxODdiZmVkNzQ1OWNiZWRkZjg5N2NmNzcwNjYzID0gJCgnPGRpdiBpZD0iaHRtbF9iNWY2MTg3YmZlZDc0NTljYmVkZGY4OTdjZjc3MDY2MyIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+TGF3cmVuY2UgUGFyayBDbHVzdGVyIDM8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2E4Y2I3NjhlOGMyMjQ1ZmNhNjZiMWUwZDE3MmQ3YTY0LnNldENvbnRlbnQoaHRtbF9iNWY2MTg3YmZlZDc0NTljYmVkZGY4OTdjZjc3MDY2Myk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8zMjM0NDM4ZWQwNjk0NTI0YjY3NGU5NmI1MDM2YTM0Yi5iaW5kUG9wdXAocG9wdXBfYThjYjc2OGU4YzIyNDVmY2E2NmIxZTBkMTcyZDdhNjQpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfNzljNzc5ZmQxMzMyNDRhNjhkOGNiNjc4NDFkNTJiMDIgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My43MTI3NTExLC03OS4zOTAxOTc1XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogIiNmZjAwMDAiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjZmYwMDAwIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzM4ZGUzODcxNmRhNjRlMzM4NzM0NTQwN2EwMDllNTYzKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzQzMThiYzA2YjIzNjQ4NDhhYWMwOTg2MmZkNDJiZjNiID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2FhOWQ2MDA3YzJmMTRhMmFhZDUzNTMxODlhN2E5YjViID0gJCgnPGRpdiBpZD0iaHRtbF9hYTlkNjAwN2MyZjE0YTJhYWQ1MzUzMTg5YTdhOWI1YiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+RGF2aXN2aWxsZSBOb3J0aCBDbHVzdGVyIDA8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzQzMThiYzA2YjIzNjQ4NDhhYWMwOTg2MmZkNDJiZjNiLnNldENvbnRlbnQoaHRtbF9hYTlkNjAwN2MyZjE0YTJhYWQ1MzUzMTg5YTdhOWI1Yik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl83OWM3NzlmZDEzMzI0NGE2OGQ4Y2I2Nzg0MWQ1MmIwMi5iaW5kUG9wdXAocG9wdXBfNDMxOGJjMDZiMjM2NDg0OGFhYzA5ODYyZmQ0MmJmM2IpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfYzIyYjE2YzVkNGQ0NGE1MWEzNDhmODIzMzg1ZTgxOTcgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My43MTUzODM0LC03OS40MDU2Nzg0MDAwMDAwMV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjZmYwMDAwIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiI2ZmMDAwMCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8zOGRlMzg3MTZkYTY0ZTMzODczNDU0MDdhMDA5ZTU2Myk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9iOGY0MWVlMzM3ODk0OTU4YWM2ZTRiMjQ5Y2UxZGJiYSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8wMWM4MTc1ODU2NGQ0ZDgwYTdiODU3OTI1M2Y0YzYzZSA9ICQoJzxkaXYgaWQ9Imh0bWxfMDFjODE3NTg1NjRkNGQ4MGE3Yjg1NzkyNTNmNGM2M2UiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPk5vcnRoIFRvcm9udG8gV2VzdCBDbHVzdGVyIDA8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2I4ZjQxZWUzMzc4OTQ5NThhYzZlNGIyNDljZTFkYmJhLnNldENvbnRlbnQoaHRtbF8wMWM4MTc1ODU2NGQ0ZDgwYTdiODU3OTI1M2Y0YzYzZSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9jMjJiMTZjNWQ0ZDQ0YTUxYTM0OGY4MjMzODVlODE5Ny5iaW5kUG9wdXAocG9wdXBfYjhmNDFlZTMzNzg5NDk1OGFjNmU0YjI0OWNlMWRiYmEpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfNzVhMGYyNzkxZmJlNDYxNjlmNmU0NWI5ZTFiYmY0ZjMgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My43MDQzMjQ0LC03OS4zODg3OTAxXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogIiNmZjAwMDAiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjZmYwMDAwIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzM4ZGUzODcxNmRhNjRlMzM4NzM0NTQwN2EwMDllNTYzKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzYwOTI5MDI5MjY2MjQ3ZGNiZGMyNDUxNzU3NmJmN2VmID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzhjYWFkYmNlYTBiMzRmMzBiMmQ0NzYxY2I3M2EyYjVlID0gJCgnPGRpdiBpZD0iaHRtbF84Y2FhZGJjZWEwYjM0ZjMwYjJkNDc2MWNiNzNhMmI1ZSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+RGF2aXN2aWxsZSBDbHVzdGVyIDA8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzYwOTI5MDI5MjY2MjQ3ZGNiZGMyNDUxNzU3NmJmN2VmLnNldENvbnRlbnQoaHRtbF84Y2FhZGJjZWEwYjM0ZjMwYjJkNDc2MWNiNzNhMmI1ZSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl83NWEwZjI3OTFmYmU0NjE2OWY2ZTQ1YjllMWJiZjRmMy5iaW5kUG9wdXAocG9wdXBfNjA5MjkwMjkyNjYyNDdkY2JkYzI0NTE3NTc2YmY3ZWYpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfNmIyMWMzNzNkY2MyNDZlZWI5ZWM3YmUyNjIwODYwMjAgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42ODk1NzQzLC03OS4zODMxNTk5MDAwMDAwMV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjODAwMGZmIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzgwMDBmZiIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8zOGRlMzg3MTZkYTY0ZTMzODczNDU0MDdhMDA5ZTU2Myk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8yMDZkMDVhY2UwYmQ0ZjI1YTc0NmVjMzA5N2JkMWVkYiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF85ZjE1ODRlNGM3Mjc0MjJlODliNTFkNWJkZjQwN2QzYSA9ICQoJzxkaXYgaWQ9Imh0bWxfOWYxNTg0ZTRjNzI3NDIyZTg5YjUxZDViZGY0MDdkM2EiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPk1vb3JlIFBhcmssIFN1bW1lcmhpbGwgRWFzdCBDbHVzdGVyIDE8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzIwNmQwNWFjZTBiZDRmMjVhNzQ2ZWMzMDk3YmQxZWRiLnNldENvbnRlbnQoaHRtbF85ZjE1ODRlNGM3Mjc0MjJlODliNTFkNWJkZjQwN2QzYSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl82YjIxYzM3M2RjYzI0NmVlYjllYzdiZTI2MjA4NjAyMC5iaW5kUG9wdXAocG9wdXBfMjA2ZDA1YWNlMGJkNGYyNWE3NDZlYzMwOTdiZDFlZGIpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfNGFjMmMzOWUwZTY2NGM5MzkzMDFlMzQwMmM5MGI0NGUgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42ODY0MTIyOTk5OTk5OSwtNzkuNDAwMDQ5M10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjZmYwMDAwIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiI2ZmMDAwMCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8zOGRlMzg3MTZkYTY0ZTMzODczNDU0MDdhMDA5ZTU2Myk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF84ZGI1ZGRlNTY4YzM0NmQyYTA1NWNiZDY5OWQ3ODNjYSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8zZTdjZWZlYjNiZmI0NDU2ODRhM2Q5ZWE3N2ZlYTRmZSA9ICQoJzxkaXYgaWQ9Imh0bWxfM2U3Y2VmZWIzYmZiNDQ1Njg0YTNkOWVhNzdmZWE0ZmUiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkRlZXIgUGFyaywgRm9yZXN0IEhpbGwgU0UsIFJhdGhuZWxseSwgU291dGggSGlsbCwgU3VtbWVyaGlsbCBXZXN0IENsdXN0ZXIgMDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfOGRiNWRkZTU2OGMzNDZkMmEwNTVjYmQ2OTlkNzgzY2Euc2V0Q29udGVudChodG1sXzNlN2NlZmViM2JmYjQ0NTY4NGEzZDllYTc3ZmVhNGZlKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzRhYzJjMzllMGU2NjRjOTM5MzAxZTM0MDJjOTBiNDRlLmJpbmRQb3B1cChwb3B1cF84ZGI1ZGRlNTY4YzM0NmQyYTA1NWNiZDY5OWQ3ODNjYSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9jMTYyYTgzZDMzN2U0YmUyOTQzNWE2YmQ4NWU0YWUyYyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY3OTU2MjYsLTc5LjM3NzUyOTQwMDAwMDAxXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogIiNmZmIzNjAiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjZmZiMzYwIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzM4ZGUzODcxNmRhNjRlMzM4NzM0NTQwN2EwMDllNTYzKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzQ0Njk5ODY5OTM0ZjQ5NmNhMmVmYzAyZGE4NTgyNjFmID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzAxNWJkNzcxOWU3NTRkZDk5Mzc5MTI1OTBiOTgzMzgyID0gJCgnPGRpdiBpZD0iaHRtbF8wMTViZDc3MTllNzU0ZGQ5OTM3OTEyNTkwYjk4MzM4MiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+Um9zZWRhbGUgQ2x1c3RlciA0PC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF80NDY5OTg2OTkzNGY0OTZjYTJlZmMwMmRhODU4MjYxZi5zZXRDb250ZW50KGh0bWxfMDE1YmQ3NzE5ZTc1NGRkOTkzNzkxMjU5MGI5ODMzODIpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfYzE2MmE4M2QzMzdlNGJlMjk0MzVhNmJkODVlNGFlMmMuYmluZFBvcHVwKHBvcHVwXzQ0Njk5ODY5OTM0ZjQ5NmNhMmVmYzAyZGE4NTgyNjFmKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2M2ZGZiMjljOGQ3YzRiNWFhYjc2NDEyNmRlZmQ5NTI3ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjY3OTY3LC03OS4zNjc2NzUzXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogIiNmZjAwMDAiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjZmYwMDAwIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzM4ZGUzODcxNmRhNjRlMzM4NzM0NTQwN2EwMDllNTYzKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzIzNDlmMWJjMTdiZTRiNTJhZGEyNzljMzU3NGJhYzNkID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2QxOThmZjZkMWY1YzRhM2NhYTRhOGZiZjY0NTI2NjlkID0gJCgnPGRpdiBpZD0iaHRtbF9kMTk4ZmY2ZDFmNWM0YTNjYWE0YThmYmY2NDUyNjY5ZCIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+Q2FiYmFnZXRvd24sIFN0LiBKYW1lcyBUb3duIENsdXN0ZXIgMDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMjM0OWYxYmMxN2JlNGI1MmFkYTI3OWMzNTc0YmFjM2Quc2V0Q29udGVudChodG1sX2QxOThmZjZkMWY1YzRhM2NhYTRhOGZiZjY0NTI2NjlkKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2M2ZGZiMjljOGQ3YzRiNWFhYjc2NDEyNmRlZmQ5NTI3LmJpbmRQb3B1cChwb3B1cF8yMzQ5ZjFiYzE3YmU0YjUyYWRhMjc5YzM1NzRiYWMzZCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8wZTM4ZjkzMzFkNmU0YmU4OTQzNGY5MzE2NGVlMWEzZCA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY2NTg1OTksLTc5LjM4MzE1OTkwMDAwMDAxXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogIiNmZjAwMDAiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjZmYwMDAwIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzM4ZGUzODcxNmRhNjRlMzM4NzM0NTQwN2EwMDllNTYzKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzgzOTJiM2E4N2EyODRiZTBiMzU4ZDA0Nzc0NDlhY2M5ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzhhZDhkNzEyNzkyMTQ3N2FhMTJkMjY4MGQ3MDQzNzA4ID0gJCgnPGRpdiBpZD0iaHRtbF84YWQ4ZDcxMjc5MjE0NzdhYTEyZDI2ODBkNzA0MzcwOCIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+Q2h1cmNoIGFuZCBXZWxsZXNsZXkgQ2x1c3RlciAwPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF84MzkyYjNhODdhMjg0YmUwYjM1OGQwNDc3NDQ5YWNjOS5zZXRDb250ZW50KGh0bWxfOGFkOGQ3MTI3OTIxNDc3YWExMmQyNjgwZDcwNDM3MDgpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfMGUzOGY5MzMxZDZlNGJlODk0MzRmOTMxNjRlZTFhM2QuYmluZFBvcHVwKHBvcHVwXzgzOTJiM2E4N2EyODRiZTBiMzU4ZDA0Nzc0NDlhY2M5KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2M5NjU2MjhmZTE5MjQ5ZmNhZGViOWViYzAzZTk3ZDcyID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjU0MjU5OSwtNzkuMzYwNjM1OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjZmYwMDAwIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiI2ZmMDAwMCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8zOGRlMzg3MTZkYTY0ZTMzODczNDU0MDdhMDA5ZTU2Myk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9kYzQ2MmIxY2U5ZTY0NDlmYjgyNGZkODE0NzY0OGYzOSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF85NTBhY2VhM2IyZDg0OTliYWI0Zjk3YWMxNGU4MWYwMiA9ICQoJzxkaXYgaWQ9Imh0bWxfOTUwYWNlYTNiMmQ4NDk5YmFiNGY5N2FjMTRlODFmMDIiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkhhcmJvdXJmcm9udCwgUmVnZW50IFBhcmsgQ2x1c3RlciAwPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9kYzQ2MmIxY2U5ZTY0NDlmYjgyNGZkODE0NzY0OGYzOS5zZXRDb250ZW50KGh0bWxfOTUwYWNlYTNiMmQ4NDk5YmFiNGY5N2FjMTRlODFmMDIpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfYzk2NTYyOGZlMTkyNDlmY2FkZWI5ZWJjMDNlOTdkNzIuYmluZFBvcHVwKHBvcHVwX2RjNDYyYjFjZTllNjQ0OWZiODI0ZmQ4MTQ3NjQ4ZjM5KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzE0YzViMmZkMzFhMTQ1NDM4YmYyOWNmMTczOTk0YWNmID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjU3MTYxOCwtNzkuMzc4OTM3MDk5OTk5OTldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiI2ZmMDAwMCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiNmZjAwMDAiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfMzhkZTM4NzE2ZGE2NGUzMzg3MzQ1NDA3YTAwOWU1NjMpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfZDgzNjU1MmYxOWEwNGFkYmFmNDgxNTVmY2I0NGUwNTggPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfN2ZjZWE2NmFiODQ1NGI3ZDg4ZjY2MjcxOWZiMTlhYTAgPSAkKCc8ZGl2IGlkPSJodG1sXzdmY2VhNjZhYjg0NTRiN2Q4OGY2NjI3MTlmYjE5YWEwIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5SeWVyc29uLCBHYXJkZW4gRGlzdHJpY3QgQ2x1c3RlciAwPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9kODM2NTUyZjE5YTA0YWRiYWY0ODE1NWZjYjQ0ZTA1OC5zZXRDb250ZW50KGh0bWxfN2ZjZWE2NmFiODQ1NGI3ZDg4ZjY2MjcxOWZiMTlhYTApOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfMTRjNWIyZmQzMWExNDU0MzhiZjI5Y2YxNzM5OTRhY2YuYmluZFBvcHVwKHBvcHVwX2Q4MzY1NTJmMTlhMDRhZGJhZjQ4MTU1ZmNiNDRlMDU4KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzljZTQzNzVlM2IwOTRkNzY5NWRiNTZjNjFiMGE4M2FhID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjUxNDkzOSwtNzkuMzc1NDE3OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjZmYwMDAwIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiI2ZmMDAwMCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8zOGRlMzg3MTZkYTY0ZTMzODczNDU0MDdhMDA5ZTU2Myk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9mNDUzM2JlYTA4Y2E0ZDQ1YjBiNjg2ZmFiYTcxMjczOSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9kM2ZlOGY4MzI2YWU0NDc3ODE1NzZhNWQ4YTQ4NTZlMCA9ICQoJzxkaXYgaWQ9Imh0bWxfZDNmZThmODMyNmFlNDQ3NzgxNTc2YTVkOGE0ODU2ZTAiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlN0LiBKYW1lcyBUb3duIENsdXN0ZXIgMDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfZjQ1MzNiZWEwOGNhNGQ0NWIwYjY4NmZhYmE3MTI3Mzkuc2V0Q29udGVudChodG1sX2QzZmU4ZjgzMjZhZTQ0Nzc4MTU3NmE1ZDhhNDg1NmUwKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzljZTQzNzVlM2IwOTRkNzY5NWRiNTZjNjFiMGE4M2FhLmJpbmRQb3B1cChwb3B1cF9mNDUzM2JlYTA4Y2E0ZDQ1YjBiNjg2ZmFiYTcxMjczOSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9mNThhZDI1ZmE2MWE0ZjE1Yjg2ZGJhMDcxOTE3NDkxMiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY0NDc3MDc5OTk5OTk5NiwtNzkuMzczMzA2NF0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjZmYwMDAwIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiI2ZmMDAwMCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8zOGRlMzg3MTZkYTY0ZTMzODczNDU0MDdhMDA5ZTU2Myk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8yZWZmYmJhMDZiNjQ0NTYzYWIzMWFiMGE0YzI5NzZiNiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF81MTg2NjRlMjRhZWE0M2UxOWVjOTEwNDJlZGY5OTMyMCA9ICQoJzxkaXYgaWQ9Imh0bWxfNTE4NjY0ZTI0YWVhNDNlMTllYzkxMDQyZWRmOTkzMjAiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkJlcmN6eSBQYXJrIENsdXN0ZXIgMDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMmVmZmJiYTA2YjY0NDU2M2FiMzFhYjBhNGMyOTc2YjYuc2V0Q29udGVudChodG1sXzUxODY2NGUyNGFlYTQzZTE5ZWM5MTA0MmVkZjk5MzIwKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2Y1OGFkMjVmYTYxYTRmMTViODZkYmEwNzE5MTc0OTEyLmJpbmRQb3B1cChwb3B1cF8yZWZmYmJhMDZiNjQ0NTYzYWIzMWFiMGE0YzI5NzZiNik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9jOTA5MjBkYjQyNjI0ODkwOTkxMTlmMWQ0OTU2OGY3YSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY1Nzk1MjQsLTc5LjM4NzM4MjZdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiI2ZmMDAwMCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiNmZjAwMDAiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfMzhkZTM4NzE2ZGE2NGUzMzg3MzQ1NDA3YTAwOWU1NjMpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfYzZhY2I3OTNmNTE5NGQxYjgyN2NhZmU2ODIxZGVmM2IgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfY2I1MjRiNDk5ZTc3NDA0Zjk5NzcyYTk0ODBiZTQzZDEgPSAkKCc8ZGl2IGlkPSJodG1sX2NiNTI0YjQ5OWU3NzQwNGY5OTc3MmE5NDgwYmU0M2QxIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5DZW50cmFsIEJheSBTdHJlZXQgQ2x1c3RlciAwPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9jNmFjYjc5M2Y1MTk0ZDFiODI3Y2FmZTY4MjFkZWYzYi5zZXRDb250ZW50KGh0bWxfY2I1MjRiNDk5ZTc3NDA0Zjk5NzcyYTk0ODBiZTQzZDEpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfYzkwOTIwZGI0MjYyNDg5MDk5MTE5ZjFkNDk1NjhmN2EuYmluZFBvcHVwKHBvcHVwX2M2YWNiNzkzZjUxOTRkMWI4MjdjYWZlNjgyMWRlZjNiKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzY0NWViN2FmYzI1NjQzODI4YTFjZDI0NzliOWFiYWU3ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjUwNTcxMjAwMDAwMDEsLTc5LjM4NDU2NzVdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiI2ZmMDAwMCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiNmZjAwMDAiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfMzhkZTM4NzE2ZGE2NGUzMzg3MzQ1NDA3YTAwOWU1NjMpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfNGVmNDFjMjQyNGUwNDg5OTkwZGQ3OWVhNDQzMmE4ZGIgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfZDdmM2YzMDM3ZjNkNGY0NDg2YWEwYWE5MmUyOWE2ZDAgPSAkKCc8ZGl2IGlkPSJodG1sX2Q3ZjNmMzAzN2YzZDRmNDQ4NmFhMGFhOTJlMjlhNmQwIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5BZGVsYWlkZSwgS2luZywgUmljaG1vbmQgQ2x1c3RlciAwPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF80ZWY0MWMyNDI0ZTA0ODk5OTBkZDc5ZWE0NDMyYThkYi5zZXRDb250ZW50KGh0bWxfZDdmM2YzMDM3ZjNkNGY0NDg2YWEwYWE5MmUyOWE2ZDApOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfNjQ1ZWI3YWZjMjU2NDM4MjhhMWNkMjQ3OWI5YWJhZTcuYmluZFBvcHVwKHBvcHVwXzRlZjQxYzI0MjRlMDQ4OTk5MGRkNzllYTQ0MzJhOGRiKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzY4ODY2MDk5OGVmNTQ0ODlhNjc5YjFjMmVjMDkyNDRiID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjQwODE1NywtNzkuMzgxNzUyMjk5OTk5OTldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiI2ZmMDAwMCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiNmZjAwMDAiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfMzhkZTM4NzE2ZGE2NGUzMzg3MzQ1NDA3YTAwOWU1NjMpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfOTM2MjdhMDQ5NDZlNGRiN2JlNjJlY2RjMjc2ZDM2MTIgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMWRhNGNjNmU0Nzc2NDBhNDkyMmI2YWFmNDA2ZTZiZGEgPSAkKCc8ZGl2IGlkPSJodG1sXzFkYTRjYzZlNDc3NjQwYTQ5MjJiNmFhZjQwNmU2YmRhIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5IYXJib3VyZnJvbnQgRWFzdCwgVG9yb250byBJc2xhbmRzLCBVbmlvbiBTdGF0aW9uIENsdXN0ZXIgMDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfOTM2MjdhMDQ5NDZlNGRiN2JlNjJlY2RjMjc2ZDM2MTIuc2V0Q29udGVudChodG1sXzFkYTRjYzZlNDc3NjQwYTQ5MjJiNmFhZjQwNmU2YmRhKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzY4ODY2MDk5OGVmNTQ0ODlhNjc5YjFjMmVjMDkyNDRiLmJpbmRQb3B1cChwb3B1cF85MzYyN2EwNDk0NmU0ZGI3YmU2MmVjZGMyNzZkMzYxMik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9hZDg0MzdkOWMyNjA0NWJiOTg3ZDA2Nzg2M2NhZWM4ZSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY0NzE3NjgsLTc5LjM4MTU3NjQwMDAwMDAxXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogIiNmZjAwMDAiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjZmYwMDAwIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzM4ZGUzODcxNmRhNjRlMzM4NzM0NTQwN2EwMDllNTYzKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzgzZGVhMDdjYjg3ZjRmZTA5MzdiYmRjZTdmZjNlNzI1ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzA5Nzc5ZjZjZTY4OTRlYTI4OGI0ZDZlZDA3NzA1ODFkID0gJCgnPGRpdiBpZD0iaHRtbF8wOTc3OWY2Y2U2ODk0ZWEyODhiNGQ2ZWQwNzcwNTgxZCIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+RGVzaWduIEV4Y2hhbmdlLCBUb3JvbnRvIERvbWluaW9uIENlbnRyZSBDbHVzdGVyIDA8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzgzZGVhMDdjYjg3ZjRmZTA5MzdiYmRjZTdmZjNlNzI1LnNldENvbnRlbnQoaHRtbF8wOTc3OWY2Y2U2ODk0ZWEyODhiNGQ2ZWQwNzcwNTgxZCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9hZDg0MzdkOWMyNjA0NWJiOTg3ZDA2Nzg2M2NhZWM4ZS5iaW5kUG9wdXAocG9wdXBfODNkZWEwN2NiODdmNGZlMDkzN2JiZGNlN2ZmM2U3MjUpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMTc3NDNjOTFiZmM1NDM2M2JkYmU2Y2Y0MzIwYzVjZGMgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NDgxOTg1LC03OS4zNzk4MTY5MDAwMDAwMV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjZmYwMDAwIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiI2ZmMDAwMCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8zOGRlMzg3MTZkYTY0ZTMzODczNDU0MDdhMDA5ZTU2Myk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9jMWIwYTkyMWJlOWI0MWM4YmVkNzAyOWU0OTQxNjkyOCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9mMjJmZmEyMDhkMDI0Yzg2ODIzNDY5NWIwN2Y2ODYwNCA9ICQoJzxkaXYgaWQ9Imh0bWxfZjIyZmZhMjA4ZDAyNGM4NjgyMzQ2OTViMDdmNjg2MDQiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkNvbW1lcmNlIENvdXJ0LCBWaWN0b3JpYSBIb3RlbCBDbHVzdGVyIDA8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2MxYjBhOTIxYmU5YjQxYzhiZWQ3MDI5ZTQ5NDE2OTI4LnNldENvbnRlbnQoaHRtbF9mMjJmZmEyMDhkMDI0Yzg2ODIzNDY5NWIwN2Y2ODYwNCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8xNzc0M2M5MWJmYzU0MzYzYmRiZTZjZjQzMjBjNWNkYy5iaW5kUG9wdXAocG9wdXBfYzFiMGE5MjFiZTliNDFjOGJlZDcwMjllNDk0MTY5MjgpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfM2IxY2FmZmRiMDI3NGYxMDk1NjhlYmYyYmE3MzgxYzIgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My43MTE2OTQ4LC03OS40MTY5MzU1OTk5OTk5OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjMDBiNWViIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzAwYjVlYiIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8zOGRlMzg3MTZkYTY0ZTMzODczNDU0MDdhMDA5ZTU2Myk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8yNTA1ZDJmNmM5YTA0MzhlOWY1YmE4ZWU1YWJmYTcxMCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8zNDJhNTZmYWEyZDk0ZTkzOTVhODZhZWJmNGZkMDcwMiA9ICQoJzxkaXYgaWQ9Imh0bWxfMzQyYTU2ZmFhMmQ5NGU5Mzk1YTg2YWViZjRmZDA3MDIiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlJvc2VsYXduIENsdXN0ZXIgMjwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMjUwNWQyZjZjOWEwNDM4ZTlmNWJhOGVlNWFiZmE3MTAuc2V0Q29udGVudChodG1sXzM0MmE1NmZhYTJkOTRlOTM5NWE4NmFlYmY0ZmQwNzAyKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzNiMWNhZmZkYjAyNzRmMTA5NTY4ZWJmMmJhNzM4MWMyLmJpbmRQb3B1cChwb3B1cF8yNTA1ZDJmNmM5YTA0MzhlOWY1YmE4ZWU1YWJmYTcxMCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9mZWIzODI3YTdmNTk0NGFiOTFiNzI0ZGJjNTYxZDQ2MiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY5Njk0NzYsLTc5LjQxMTMwNzIwMDAwMDAxXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogIiNmZjAwMDAiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjZmYwMDAwIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzM4ZGUzODcxNmRhNjRlMzM4NzM0NTQwN2EwMDllNTYzKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2NkM2JmZWQ2MWZmNDRiODQ5Nzk2MWY4ZjFjMmJmYTE2ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzgyMWJkNTA0YzRkYjQ4MjQ4NTBlYzg4NzQ5NDVmYzQ5ID0gJCgnPGRpdiBpZD0iaHRtbF84MjFiZDUwNGM0ZGI0ODI0ODUwZWM4ODc0OTQ1ZmM0OSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+Rm9yZXN0IEhpbGwgTm9ydGgsIEZvcmVzdCBIaWxsIFdlc3QgQ2x1c3RlciAwPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9jZDNiZmVkNjFmZjQ0Yjg0OTc5NjFmOGYxYzJiZmExNi5zZXRDb250ZW50KGh0bWxfODIxYmQ1MDRjNGRiNDgyNDg1MGVjODg3NDk0NWZjNDkpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfZmViMzgyN2E3ZjU5NDRhYjkxYjcyNGRiYzU2MWQ0NjIuYmluZFBvcHVwKHBvcHVwX2NkM2JmZWQ2MWZmNDRiODQ5Nzk2MWY4ZjFjMmJmYTE2KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzIxMGZlZWFkZTYzMzRjYjY4MjJlM2FmNmFhODBlZWVhID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjcyNzA5NywtNzkuNDA1Njc4NDAwMDAwMDFdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiI2ZmMDAwMCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiNmZjAwMDAiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfMzhkZTM4NzE2ZGE2NGUzMzg3MzQ1NDA3YTAwOWU1NjMpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfNzQ5NmI5MWQ2Y2ViNGU4YjkzZjhlZmZkNDc3OTFlMjYgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfYTc5ZjM3OTcxZTE5NDVkYjgwZjU0YTFiZTBmMzAzOTAgPSAkKCc8ZGl2IGlkPSJodG1sX2E3OWYzNzk3MWUxOTQ1ZGI4MGY1NGExYmUwZjMwMzkwIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5UaGUgQW5uZXgsIE5vcnRoIE1pZHRvd24sIFlvcmt2aWxsZSBDbHVzdGVyIDA8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzc0OTZiOTFkNmNlYjRlOGI5M2Y4ZWZmZDQ3NzkxZTI2LnNldENvbnRlbnQoaHRtbF9hNzlmMzc5NzFlMTk0NWRiODBmNTRhMWJlMGYzMDM5MCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8yMTBmZWVhZGU2MzM0Y2I2ODIyZTNhZjZhYTgwZWVlYS5iaW5kUG9wdXAocG9wdXBfNzQ5NmI5MWQ2Y2ViNGU4YjkzZjhlZmZkNDc3OTFlMjYpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfYWMyMWI4OTA0NjVjNDRmNGJiOWY0M2JmMTU0MDg2ZjggPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NjI2OTU2LC03OS40MDAwNDkzXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogIiNmZjAwMDAiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjZmYwMDAwIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzM4ZGUzODcxNmRhNjRlMzM4NzM0NTQwN2EwMDllNTYzKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2VlNWJhNjQxOWI2ZjQzOTFhNmRkMGU3MGM4YWY0NzJiID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzEzNmIyMDE0NTQxMDQ3NWNhYjAzNGY0NWJjMjAzM2E0ID0gJCgnPGRpdiBpZD0iaHRtbF8xMzZiMjAxNDU0MTA0NzVjYWIwMzRmNDViYzIwMzNhNCIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+SGFyYm9yZCwgVW5pdmVyc2l0eSBvZiBUb3JvbnRvIENsdXN0ZXIgMDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfZWU1YmE2NDE5YjZmNDM5MWE2ZGQwZTcwYzhhZjQ3MmIuc2V0Q29udGVudChodG1sXzEzNmIyMDE0NTQxMDQ3NWNhYjAzNGY0NWJjMjAzM2E0KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2FjMjFiODkwNDY1YzQ0ZjRiYjlmNDNiZjE1NDA4NmY4LmJpbmRQb3B1cChwb3B1cF9lZTViYTY0MTliNmY0MzkxYTZkZDBlNzBjOGFmNDcyYik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9mNGU3YjMyNzRkZDg0YmEyODkyMTI4MTAyNjAwYTQwZSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY1MzIwNTcsLTc5LjQwMDA0OTNdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiI2ZmMDAwMCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiNmZjAwMDAiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfMzhkZTM4NzE2ZGE2NGUzMzg3MzQ1NDA3YTAwOWU1NjMpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfNGMxNmU5YmFjODZkNDM0YWE0MDg0NzNjNzQzZWE3MDIgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNGIyZTAyNTE0OWVkNDViMmJiMDQzYjMxYzdhMzViYzMgPSAkKCc8ZGl2IGlkPSJodG1sXzRiMmUwMjUxNDllZDQ1YjJiYjA0M2IzMWM3YTM1YmMzIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5DaGluYXRvd24sIEdyYW5nZSBQYXJrLCBLZW5zaW5ndG9uIE1hcmtldCBDbHVzdGVyIDA8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzRjMTZlOWJhYzg2ZDQzNGFhNDA4NDczYzc0M2VhNzAyLnNldENvbnRlbnQoaHRtbF80YjJlMDI1MTQ5ZWQ0NWIyYmIwNDNiMzFjN2EzNWJjMyk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9mNGU3YjMyNzRkZDg0YmEyODkyMTI4MTAyNjAwYTQwZS5iaW5kUG9wdXAocG9wdXBfNGMxNmU5YmFjODZkNDM0YWE0MDg0NzNjNzQzZWE3MDIpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMzc4ZjdiNGFhNzJlNGNkYmI3OWQ4YWRkMGEwMmI0YzQgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42Mjg5NDY3LC03OS4zOTQ0MTk5XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogIiNmZjAwMDAiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjZmYwMDAwIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzM4ZGUzODcxNmRhNjRlMzM4NzM0NTQwN2EwMDllNTYzKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzUwMTk4NWRmYzQ2YjRmMDZiNWM5MTE4ZTgxODg5YTE3ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzBhZTUyZjI0MjIxZjQ3NjliMzgwMjFkNWVhZGQzOGYwID0gJCgnPGRpdiBpZD0iaHRtbF8wYWU1MmYyNDIyMWY0NzY5YjM4MDIxZDVlYWRkMzhmMCIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+Q04gVG93ZXIsIEJhdGh1cnN0IFF1YXksIElzbGFuZCBhaXJwb3J0LCBIYXJib3VyZnJvbnQgV2VzdCwgS2luZyBhbmQgU3BhZGluYSwgUmFpbHdheSBMYW5kcywgU291dGggTmlhZ2FyYSBDbHVzdGVyIDA8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzUwMTk4NWRmYzQ2YjRmMDZiNWM5MTE4ZTgxODg5YTE3LnNldENvbnRlbnQoaHRtbF8wYWU1MmYyNDIyMWY0NzY5YjM4MDIxZDVlYWRkMzhmMCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8zNzhmN2I0YWE3MmU0Y2RiYjc5ZDhhZGQwYTAyYjRjNC5iaW5kUG9wdXAocG9wdXBfNTAxOTg1ZGZjNDZiNGYwNmI1YzkxMThlODE4ODlhMTcpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfZTY0MDJjYjA0ODhmNDIxZTg1YzkyODVmNmFlZTdkYmEgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NDY0MzUyLC03OS4zNzQ4NDU5OTk5OTk5OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjZmYwMDAwIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiI2ZmMDAwMCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8zOGRlMzg3MTZkYTY0ZTMzODczNDU0MDdhMDA5ZTU2Myk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF80NDFhMjcyN2UzMTU0ODM1YWU2NmFlZDE5YWM5Mjc1ZCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8xMWQ1ZmFmODVhMWY0NzQ3ODE4NGQ0MDRjMzczZTA4NCA9ICQoJzxkaXYgaWQ9Imh0bWxfMTFkNWZhZjg1YTFmNDc0NzgxODRkNDA0YzM3M2UwODQiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlN0biBBIFBPIEJveGVzIDI1IFRoZSBFc3BsYW5hZGUgQ2x1c3RlciAwPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF80NDFhMjcyN2UzMTU0ODM1YWU2NmFlZDE5YWM5Mjc1ZC5zZXRDb250ZW50KGh0bWxfMTFkNWZhZjg1YTFmNDc0NzgxODRkNDA0YzM3M2UwODQpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfZTY0MDJjYjA0ODhmNDIxZTg1YzkyODVmNmFlZTdkYmEuYmluZFBvcHVwKHBvcHVwXzQ0MWEyNzI3ZTMxNTQ4MzVhZTY2YWVkMTlhYzkyNzVkKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2ZlNWFkOWVkNmI5YzRjNzJhZjZiNDRhZDBmMTg2NmIwID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjQ4NDI5MiwtNzkuMzgyMjgwMl0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjZmYwMDAwIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiI2ZmMDAwMCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8zOGRlMzg3MTZkYTY0ZTMzODczNDU0MDdhMDA5ZTU2Myk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8zYzg3YzIwYTQwN2U0N2U0OGRmYTRmZmYwNzJhMWYzMCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8yZWMxNzdjNDQ2M2I0ZDUyOWY4NTBhYWQ3ZTA0MjFiYiA9ICQoJzxkaXYgaWQ9Imh0bWxfMmVjMTc3YzQ0NjNiNGQ1MjlmODUwYWFkN2UwNDIxYmIiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkZpcnN0IENhbmFkaWFuIFBsYWNlLCBVbmRlcmdyb3VuZCBjaXR5IENsdXN0ZXIgMDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfM2M4N2MyMGE0MDdlNDdlNDhkZmE0ZmZmMDcyYTFmMzAuc2V0Q29udGVudChodG1sXzJlYzE3N2M0NDYzYjRkNTI5Zjg1MGFhZDdlMDQyMWJiKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2ZlNWFkOWVkNmI5YzRjNzJhZjZiNDRhZDBmMTg2NmIwLmJpbmRQb3B1cChwb3B1cF8zYzg3YzIwYTQwN2U0N2U0OGRmYTRmZmYwNzJhMWYzMCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8zNTlkYmNmZjQ5Nzk0OTlmYjVhMWQ3YjAyZjczNDliZiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY2OTU0MiwtNzkuNDIyNTYzN10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjZmYwMDAwIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiI2ZmMDAwMCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8zOGRlMzg3MTZkYTY0ZTMzODczNDU0MDdhMDA5ZTU2Myk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9mMGFlYzU3ZjRiNWY0MmUyYWVmZWY1ZGM5OGQ4ZDg1ZiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9kMWFmNDBkOWQyMzg0NzA5YmI5YjE0YmY2ODdmMDljYiA9ICQoJzxkaXYgaWQ9Imh0bWxfZDFhZjQwZDlkMjM4NDcwOWJiOWIxNGJmNjg3ZjA5Y2IiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkNocmlzdGllIENsdXN0ZXIgMDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfZjBhZWM1N2Y0YjVmNDJlMmFlZmVmNWRjOThkOGQ4NWYuc2V0Q29udGVudChodG1sX2QxYWY0MGQ5ZDIzODQ3MDliYjliMTRiZjY4N2YwOWNiKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzM1OWRiY2ZmNDk3OTQ5OWZiNWExZDdiMDJmNzM0OWJmLmJpbmRQb3B1cChwb3B1cF9mMGFlYzU3ZjRiNWY0MmUyYWVmZWY1ZGM5OGQ4ZDg1Zik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8zZDk1MWZmYWM1ZDg0MGE2ODA5MmM4NTlkZTlhMGU4NCA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY2OTAwNTEwMDAwMDAxLC03OS40NDIyNTkzXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogIiNmZjAwMDAiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjZmYwMDAwIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzM4ZGUzODcxNmRhNjRlMzM4NzM0NTQwN2EwMDllNTYzKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzk3YjAyOGFhYTAwMzQ0YmViYjNiMjgzM2M1M2YxZDlhID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzU2MjgxMDZmNGVhODQyM2M4Yjg1MDViMmViMWExYTc3ID0gJCgnPGRpdiBpZD0iaHRtbF81NjI4MTA2ZjRlYTg0MjNjOGI4NTA1YjJlYjFhMWE3NyIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+RG92ZXJjb3VydCBWaWxsYWdlLCBEdWZmZXJpbiBDbHVzdGVyIDA8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzk3YjAyOGFhYTAwMzQ0YmViYjNiMjgzM2M1M2YxZDlhLnNldENvbnRlbnQoaHRtbF81NjI4MTA2ZjRlYTg0MjNjOGI4NTA1YjJlYjFhMWE3Nyk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8zZDk1MWZmYWM1ZDg0MGE2ODA5MmM4NTlkZTlhMGU4NC5iaW5kUG9wdXAocG9wdXBfOTdiMDI4YWFhMDAzNDRiZWJiM2IyODMzYzUzZjFkOWEpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMDFkZTk5ODViNmYyNGZhODhlZTRiZWI2ZjkzOTI3YjMgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NDc5MjY3MDAwMDAwMDYsLTc5LjQxOTc0OTddLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiI2ZmMDAwMCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiNmZjAwMDAiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfMzhkZTM4NzE2ZGE2NGUzMzg3MzQ1NDA3YTAwOWU1NjMpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfNjVhN2MyY2I1YjA4NGIyZWI5MDBkMDRhZjBlNTQyMzEgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfZmJiYmQwZWQwNDhkNDg2MTkwZjBjMjg3ZDJiNzBjNzcgPSAkKCc8ZGl2IGlkPSJodG1sX2ZiYmJkMGVkMDQ4ZDQ4NjE5MGYwYzI4N2QyYjcwYzc3IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5MaXR0bGUgUG9ydHVnYWwsIFRyaW5pdHkgQ2x1c3RlciAwPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF82NWE3YzJjYjViMDg0YjJlYjkwMGQwNGFmMGU1NDIzMS5zZXRDb250ZW50KGh0bWxfZmJiYmQwZWQwNDhkNDg2MTkwZjBjMjg3ZDJiNzBjNzcpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfMDFkZTk5ODViNmYyNGZhODhlZTRiZWI2ZjkzOTI3YjMuYmluZFBvcHVwKHBvcHVwXzY1YTdjMmNiNWIwODRiMmViOTAwZDA0YWYwZTU0MjMxKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzk1ZDFmZjdiOTU5OTRlNDZiZTFjOWRjNTk4OGI0ODA4ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjM2ODQ3MiwtNzkuNDI4MTkxNDAwMDAwMDJdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiI2ZmMDAwMCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiNmZjAwMDAiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfMzhkZTM4NzE2ZGE2NGUzMzg3MzQ1NDA3YTAwOWU1NjMpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfNTgxZmU3NDZjZjE1NDY3MmFkNzJhNWQ4YWUzNTU0ZTMgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMjBhMDkzMzA2Mjg0NGU2Y2JmMWNhMmJjMDhkNTZjNWMgPSAkKCc8ZGl2IGlkPSJodG1sXzIwYTA5MzMwNjI4NDRlNmNiZjFjYTJiYzA4ZDU2YzVjIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5Ccm9ja3RvbiwgRXhoaWJpdGlvbiBQbGFjZSwgUGFya2RhbGUgVmlsbGFnZSBDbHVzdGVyIDA8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzU4MWZlNzQ2Y2YxNTQ2NzJhZDcyYTVkOGFlMzU1NGUzLnNldENvbnRlbnQoaHRtbF8yMGEwOTMzMDYyODQ0ZTZjYmYxY2EyYmMwOGQ1NmM1Yyk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl85NWQxZmY3Yjk1OTk0ZTQ2YmUxYzlkYzU5ODhiNDgwOC5iaW5kUG9wdXAocG9wdXBfNTgxZmU3NDZjZjE1NDY3MmFkNzJhNWQ4YWUzNTU0ZTMpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfYTQxZmRkNjkyYTFmNDNjNGEwMzBlYTBlODQ0NjgxNDggPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NjE2MDgzLC03OS40NjQ3NjMyOTk5OTk5OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjZmYwMDAwIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiI2ZmMDAwMCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8zOGRlMzg3MTZkYTY0ZTMzODczNDU0MDdhMDA5ZTU2Myk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8zYzE4NTRmODdiNTM0MmIxYWFkZDBhNjViN2RhMzVkOSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF81OWRiOGRlYjljZjA0YjkzOWUyMjU1N2MxNGJhZjBlZCA9ICQoJzxkaXYgaWQ9Imh0bWxfNTlkYjhkZWI5Y2YwNGI5MzllMjI1NTdjMTRiYWYwZWQiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkhpZ2ggUGFyaywgVGhlIEp1bmN0aW9uIFNvdXRoIENsdXN0ZXIgMDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfM2MxODU0Zjg3YjUzNDJiMWFhZGQwYTY1YjdkYTM1ZDkuc2V0Q29udGVudChodG1sXzU5ZGI4ZGViOWNmMDRiOTM5ZTIyNTU3YzE0YmFmMGVkKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2E0MWZkZDY5MmExZjQzYzRhMDMwZWEwZTg0NDY4MTQ4LmJpbmRQb3B1cChwb3B1cF8zYzE4NTRmODdiNTM0MmIxYWFkZDBhNjViN2RhMzVkOSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl83ZjYzMjc1Y2NkZjg0NjkwOTEwMTU5M2VmZWQ4ODFjNiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY0ODk1OTcsLTc5LjQ1NjMyNV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjZmYwMDAwIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiI2ZmMDAwMCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8zOGRlMzg3MTZkYTY0ZTMzODczNDU0MDdhMDA5ZTU2Myk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9lOWM2MmE1ZTU4NDk0ZTlhYTM3NjdlZTJlZWMxODkxNSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF80N2I5Y2FiODYzYTA0NmM3OTEzN2UwYjMwMTlkZTRkYyA9ICQoJzxkaXYgaWQ9Imh0bWxfNDdiOWNhYjg2M2EwNDZjNzkxMzdlMGIzMDE5ZGU0ZGMiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlBhcmtkYWxlLCBSb25jZXN2YWxsZXMgQ2x1c3RlciAwPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9lOWM2MmE1ZTU4NDk0ZTlhYTM3NjdlZTJlZWMxODkxNS5zZXRDb250ZW50KGh0bWxfNDdiOWNhYjg2M2EwNDZjNzkxMzdlMGIzMDE5ZGU0ZGMpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfN2Y2MzI3NWNjZGY4NDY5MDkxMDE1OTNlZmVkODgxYzYuYmluZFBvcHVwKHBvcHVwX2U5YzYyYTVlNTg0OTRlOWFhMzc2N2VlMmVlYzE4OTE1KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzFiNDc4OWE4OGJjMDRlMmVhMDJjYzQ3MmU4OWZkNzJjID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjUxNTcwNiwtNzkuNDg0NDQ5OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjZmYwMDAwIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiI2ZmMDAwMCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8zOGRlMzg3MTZkYTY0ZTMzODczNDU0MDdhMDA5ZTU2Myk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9hNGE4YmRlMGEzMjc0Zjk0YmY3ODIwNmY3OTAzNWYzNSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF83MmRmMDFiZjI3MTU0ZjBjOGM4Y2JjNDE3N2VkMzFlNiA9ICQoJzxkaXYgaWQ9Imh0bWxfNzJkZjAxYmYyNzE1NGYwYzhjOGNiYzQxNzdlZDMxZTYiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlJ1bm55bWVkZSwgU3dhbnNlYSBDbHVzdGVyIDA8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2E0YThiZGUwYTMyNzRmOTRiZjc4MjA2Zjc5MDM1ZjM1LnNldENvbnRlbnQoaHRtbF83MmRmMDFiZjI3MTU0ZjBjOGM4Y2JjNDE3N2VkMzFlNik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8xYjQ3ODlhODhiYzA0ZTJlYTAyY2M0NzJlODlmZDcyYy5iaW5kUG9wdXAocG9wdXBfYTRhOGJkZTBhMzI3NGY5NGJmNzgyMDZmNzkwMzVmMzUpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfYjljNzZiNjkyZTM5NDg4YmE3Y2VmN2QzMjY3NDUzYmYgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NjI3NDM5LC03OS4zMjE1NThdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiI2ZmMDAwMCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiNmZjAwMDAiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfMzhkZTM4NzE2ZGE2NGUzMzg3MzQ1NDA3YTAwOWU1NjMpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfZTkwNzQ5YTY0N2Q0NDNhMWE4OTM4NmMyZmQxMTg5OTggPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfM2YzMmQzYWFhNjg2NDZkMmI0NmMzNjk0ZTkzYzNhNjYgPSAkKCc8ZGl2IGlkPSJodG1sXzNmMzJkM2FhYTY4NjQ2ZDJiNDZjMzY5NGU5M2MzYTY2IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5CdXNpbmVzcyBSZXBseSBNYWlsIFByb2Nlc3NpbmcgQ2VudHJlIDk2OSBFYXN0ZXJuIENsdXN0ZXIgMDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfZTkwNzQ5YTY0N2Q0NDNhMWE4OTM4NmMyZmQxMTg5OTguc2V0Q29udGVudChodG1sXzNmMzJkM2FhYTY4NjQ2ZDJiNDZjMzY5NGU5M2MzYTY2KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2I5Yzc2YjY5MmUzOTQ4OGJhN2NlZjdkMzI2NzQ1M2JmLmJpbmRQb3B1cChwb3B1cF9lOTA3NDlhNjQ3ZDQ0M2ExYTg5Mzg2YzJmZDExODk5OCk7CgogICAgICAgICAgICAKICAgICAgICAKPC9zY3JpcHQ+" style="position:absolute;width:100%;height:100%;left:0;top:0;border:none !important;" allowfullscreen webkitallowfullscreen mozallowfullscreen></iframe></div></div>



### We can see that 5 clusters were created but really most of the neighborhoods belong to the first cluster and this has a reasonable explanation

### First of all let's see how the datframes are with the information of each cluster and analyze them

#### Cluster 1


```python
Toronto_merged.loc[Toronto_merged['Cluster Labels'] == 0, Toronto_merged.columns[[1] + list(range(5, Toronto_merged.shape[1]))]]
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Borough</th>
      <th>Cluster Labels</th>
      <th>1st Most Common Venue</th>
      <th>2nd Most Common Venue</th>
      <th>3rd Most Common Venue</th>
      <th>4th Most Common Venue</th>
      <th>5th Most Common Venue</th>
      <th>6th Most Common Venue</th>
      <th>7th Most Common Venue</th>
      <th>8th Most Common Venue</th>
      <th>9th Most Common Venue</th>
      <th>10th Most Common Venue</th>
    </tr>
    <tr>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>East Toronto</td>
      <td>0</td>
      <td>Health Food Store</td>
      <td>Trail</td>
      <td>Pub</td>
      <td>Diner</td>
      <td>Fast Food Restaurant</td>
      <td>Farmers Market</td>
      <td>Falafel Restaurant</td>
      <td>Event Space</td>
      <td>Ethiopian Restaurant</td>
      <td>Electronics Store</td>
    </tr>
    <tr>
      <td>1</td>
      <td>East Toronto</td>
      <td>0</td>
      <td>Greek Restaurant</td>
      <td>Coffee Shop</td>
      <td>Italian Restaurant</td>
      <td>Ice Cream Shop</td>
      <td>Furniture / Home Store</td>
      <td>Bookstore</td>
      <td>Pub</td>
      <td>Indian Restaurant</td>
      <td>Sports Bar</td>
      <td>Spa</td>
    </tr>
    <tr>
      <td>2</td>
      <td>East Toronto</td>
      <td>0</td>
      <td>Pet Store</td>
      <td>Park</td>
      <td>Brewery</td>
      <td>Sandwich Place</td>
      <td>Burger Joint</td>
      <td>Burrito Place</td>
      <td>Pub</td>
      <td>Pizza Place</td>
      <td>Movie Theater</td>
      <td>Sushi Restaurant</td>
    </tr>
    <tr>
      <td>3</td>
      <td>East Toronto</td>
      <td>0</td>
      <td>Café</td>
      <td>Coffee Shop</td>
      <td>Bakery</td>
      <td>Italian Restaurant</td>
      <td>American Restaurant</td>
      <td>Yoga Studio</td>
      <td>Comfort Food Restaurant</td>
      <td>Seafood Restaurant</td>
      <td>Brewery</td>
      <td>Sandwich Place</td>
    </tr>
    <tr>
      <td>5</td>
      <td>Central Toronto</td>
      <td>0</td>
      <td>Food &amp; Drink Shop</td>
      <td>Gym</td>
      <td>Park</td>
      <td>Breakfast Spot</td>
      <td>Asian Restaurant</td>
      <td>Hotel</td>
      <td>Clothing Store</td>
      <td>Sandwich Place</td>
      <td>Wings Joint</td>
      <td>Doner Restaurant</td>
    </tr>
    <tr>
      <td>6</td>
      <td>Central Toronto</td>
      <td>0</td>
      <td>Clothing Store</td>
      <td>Sporting Goods Shop</td>
      <td>Coffee Shop</td>
      <td>Mexican Restaurant</td>
      <td>Diner</td>
      <td>Dessert Shop</td>
      <td>Park</td>
      <td>Gym / Fitness Center</td>
      <td>Furniture / Home Store</td>
      <td>Chinese Restaurant</td>
    </tr>
    <tr>
      <td>7</td>
      <td>Central Toronto</td>
      <td>0</td>
      <td>Dessert Shop</td>
      <td>Sandwich Place</td>
      <td>Gym</td>
      <td>Pizza Place</td>
      <td>Sushi Restaurant</td>
      <td>Café</td>
      <td>Coffee Shop</td>
      <td>Italian Restaurant</td>
      <td>Japanese Restaurant</td>
      <td>Flower Shop</td>
    </tr>
    <tr>
      <td>9</td>
      <td>Central Toronto</td>
      <td>0</td>
      <td>Coffee Shop</td>
      <td>Pub</td>
      <td>American Restaurant</td>
      <td>Restaurant</td>
      <td>Sports Bar</td>
      <td>Bagel Shop</td>
      <td>Supermarket</td>
      <td>Sushi Restaurant</td>
      <td>Liquor Store</td>
      <td>Fried Chicken Joint</td>
    </tr>
    <tr>
      <td>11</td>
      <td>Downtown Toronto</td>
      <td>0</td>
      <td>Coffee Shop</td>
      <td>Restaurant</td>
      <td>Café</td>
      <td>Pub</td>
      <td>Italian Restaurant</td>
      <td>Bakery</td>
      <td>Pizza Place</td>
      <td>Liquor Store</td>
      <td>Gastropub</td>
      <td>Caribbean Restaurant</td>
    </tr>
    <tr>
      <td>12</td>
      <td>Downtown Toronto</td>
      <td>0</td>
      <td>Coffee Shop</td>
      <td>Gay Bar</td>
      <td>Japanese Restaurant</td>
      <td>Sushi Restaurant</td>
      <td>Restaurant</td>
      <td>Men's Store</td>
      <td>Gym</td>
      <td>Fast Food Restaurant</td>
      <td>Dance Studio</td>
      <td>Mediterranean Restaurant</td>
    </tr>
    <tr>
      <td>13</td>
      <td>Downtown Toronto</td>
      <td>0</td>
      <td>Coffee Shop</td>
      <td>Bakery</td>
      <td>Park</td>
      <td>Café</td>
      <td>Pub</td>
      <td>Mexican Restaurant</td>
      <td>Breakfast Spot</td>
      <td>Theater</td>
      <td>Gym / Fitness Center</td>
      <td>Historic Site</td>
    </tr>
    <tr>
      <td>14</td>
      <td>Downtown Toronto</td>
      <td>0</td>
      <td>Coffee Shop</td>
      <td>Clothing Store</td>
      <td>Middle Eastern Restaurant</td>
      <td>Cosmetics Shop</td>
      <td>Café</td>
      <td>Diner</td>
      <td>Pizza Place</td>
      <td>Sporting Goods Shop</td>
      <td>Bookstore</td>
      <td>Italian Restaurant</td>
    </tr>
    <tr>
      <td>15</td>
      <td>Downtown Toronto</td>
      <td>0</td>
      <td>Coffee Shop</td>
      <td>Café</td>
      <td>Hotel</td>
      <td>Restaurant</td>
      <td>Italian Restaurant</td>
      <td>Bakery</td>
      <td>Cosmetics Shop</td>
      <td>Cocktail Bar</td>
      <td>Clothing Store</td>
      <td>Breakfast Spot</td>
    </tr>
    <tr>
      <td>16</td>
      <td>Downtown Toronto</td>
      <td>0</td>
      <td>Coffee Shop</td>
      <td>Cocktail Bar</td>
      <td>Bakery</td>
      <td>Steakhouse</td>
      <td>Cheese Shop</td>
      <td>Farmers Market</td>
      <td>Café</td>
      <td>Beer Bar</td>
      <td>Seafood Restaurant</td>
      <td>Comfort Food Restaurant</td>
    </tr>
    <tr>
      <td>17</td>
      <td>Downtown Toronto</td>
      <td>0</td>
      <td>Coffee Shop</td>
      <td>Italian Restaurant</td>
      <td>Café</td>
      <td>Middle Eastern Restaurant</td>
      <td>Ice Cream Shop</td>
      <td>Sandwich Place</td>
      <td>Burger Joint</td>
      <td>Indian Restaurant</td>
      <td>Gym / Fitness Center</td>
      <td>Sushi Restaurant</td>
    </tr>
    <tr>
      <td>18</td>
      <td>Downtown Toronto</td>
      <td>0</td>
      <td>Coffee Shop</td>
      <td>Café</td>
      <td>Steakhouse</td>
      <td>Bar</td>
      <td>Thai Restaurant</td>
      <td>Hotel</td>
      <td>Burger Joint</td>
      <td>American Restaurant</td>
      <td>Restaurant</td>
      <td>Cosmetics Shop</td>
    </tr>
    <tr>
      <td>19</td>
      <td>Downtown Toronto</td>
      <td>0</td>
      <td>Coffee Shop</td>
      <td>Hotel</td>
      <td>Aquarium</td>
      <td>Café</td>
      <td>Brewery</td>
      <td>Fried Chicken Joint</td>
      <td>Scenic Lookout</td>
      <td>History Museum</td>
      <td>Restaurant</td>
      <td>Sporting Goods Shop</td>
    </tr>
    <tr>
      <td>20</td>
      <td>Downtown Toronto</td>
      <td>0</td>
      <td>Coffee Shop</td>
      <td>Café</td>
      <td>Hotel</td>
      <td>Restaurant</td>
      <td>American Restaurant</td>
      <td>Deli / Bodega</td>
      <td>Bar</td>
      <td>Bakery</td>
      <td>Gastropub</td>
      <td>Italian Restaurant</td>
    </tr>
    <tr>
      <td>21</td>
      <td>Downtown Toronto</td>
      <td>0</td>
      <td>Coffee Shop</td>
      <td>Café</td>
      <td>Hotel</td>
      <td>Restaurant</td>
      <td>American Restaurant</td>
      <td>Gastropub</td>
      <td>Steakhouse</td>
      <td>Seafood Restaurant</td>
      <td>Gym</td>
      <td>Italian Restaurant</td>
    </tr>
    <tr>
      <td>23</td>
      <td>Central Toronto</td>
      <td>0</td>
      <td>Trail</td>
      <td>Mexican Restaurant</td>
      <td>Jewelry Store</td>
      <td>Sushi Restaurant</td>
      <td>Wings Joint</td>
      <td>Dog Run</td>
      <td>Fast Food Restaurant</td>
      <td>Farmers Market</td>
      <td>Falafel Restaurant</td>
      <td>Event Space</td>
    </tr>
    <tr>
      <td>24</td>
      <td>Central Toronto</td>
      <td>0</td>
      <td>Café</td>
      <td>Sandwich Place</td>
      <td>Coffee Shop</td>
      <td>Pizza Place</td>
      <td>BBQ Joint</td>
      <td>Indian Restaurant</td>
      <td>Jewish Restaurant</td>
      <td>Asian Restaurant</td>
      <td>Flower Shop</td>
      <td>Pub</td>
    </tr>
    <tr>
      <td>25</td>
      <td>Downtown Toronto</td>
      <td>0</td>
      <td>Café</td>
      <td>Bakery</td>
      <td>Japanese Restaurant</td>
      <td>Bookstore</td>
      <td>Restaurant</td>
      <td>Bar</td>
      <td>Dessert Shop</td>
      <td>Chinese Restaurant</td>
      <td>Poutine Place</td>
      <td>Pub</td>
    </tr>
    <tr>
      <td>26</td>
      <td>Downtown Toronto</td>
      <td>0</td>
      <td>Café</td>
      <td>Vegetarian / Vegan Restaurant</td>
      <td>Bar</td>
      <td>Dumpling Restaurant</td>
      <td>Vietnamese Restaurant</td>
      <td>Chinese Restaurant</td>
      <td>Bakery</td>
      <td>Mexican Restaurant</td>
      <td>Coffee Shop</td>
      <td>Dessert Shop</td>
    </tr>
    <tr>
      <td>27</td>
      <td>Downtown Toronto</td>
      <td>0</td>
      <td>Airport Terminal</td>
      <td>Airport Lounge</td>
      <td>Airport Service</td>
      <td>Sculpture Garden</td>
      <td>Coffee Shop</td>
      <td>Plane</td>
      <td>Boat or Ferry</td>
      <td>Boutique</td>
      <td>Bar</td>
      <td>Airport Gate</td>
    </tr>
    <tr>
      <td>28</td>
      <td>Downtown Toronto</td>
      <td>0</td>
      <td>Coffee Shop</td>
      <td>Restaurant</td>
      <td>Café</td>
      <td>Seafood Restaurant</td>
      <td>Beer Bar</td>
      <td>Hotel</td>
      <td>Italian Restaurant</td>
      <td>Cocktail Bar</td>
      <td>Art Gallery</td>
      <td>Breakfast Spot</td>
    </tr>
    <tr>
      <td>29</td>
      <td>Downtown Toronto</td>
      <td>0</td>
      <td>Coffee Shop</td>
      <td>Café</td>
      <td>Hotel</td>
      <td>Restaurant</td>
      <td>Steakhouse</td>
      <td>Gym</td>
      <td>Gastropub</td>
      <td>Seafood Restaurant</td>
      <td>Bar</td>
      <td>Burger Joint</td>
    </tr>
    <tr>
      <td>30</td>
      <td>Downtown Toronto</td>
      <td>0</td>
      <td>Café</td>
      <td>Grocery Store</td>
      <td>Park</td>
      <td>Convenience Store</td>
      <td>Nightclub</td>
      <td>Restaurant</td>
      <td>Italian Restaurant</td>
      <td>Baby Store</td>
      <td>Diner</td>
      <td>Athletics &amp; Sports</td>
    </tr>
    <tr>
      <td>31</td>
      <td>West Toronto</td>
      <td>0</td>
      <td>Pharmacy</td>
      <td>Supermarket</td>
      <td>Bakery</td>
      <td>Middle Eastern Restaurant</td>
      <td>Music Venue</td>
      <td>Park</td>
      <td>Pizza Place</td>
      <td>Coffee Shop</td>
      <td>Café</td>
      <td>Brewery</td>
    </tr>
    <tr>
      <td>32</td>
      <td>West Toronto</td>
      <td>0</td>
      <td>Bar</td>
      <td>Coffee Shop</td>
      <td>Asian Restaurant</td>
      <td>Vietnamese Restaurant</td>
      <td>Café</td>
      <td>Boutique</td>
      <td>Men's Store</td>
      <td>Cocktail Bar</td>
      <td>Pizza Place</td>
      <td>French Restaurant</td>
    </tr>
    <tr>
      <td>33</td>
      <td>West Toronto</td>
      <td>0</td>
      <td>Breakfast Spot</td>
      <td>Café</td>
      <td>Coffee Shop</td>
      <td>Furniture / Home Store</td>
      <td>Burrito Place</td>
      <td>Convenience Store</td>
      <td>Restaurant</td>
      <td>Caribbean Restaurant</td>
      <td>Stadium</td>
      <td>Bar</td>
    </tr>
    <tr>
      <td>34</td>
      <td>West Toronto</td>
      <td>0</td>
      <td>Café</td>
      <td>Mexican Restaurant</td>
      <td>Fried Chicken Joint</td>
      <td>Music Venue</td>
      <td>Bakery</td>
      <td>Italian Restaurant</td>
      <td>Discount Store</td>
      <td>Speakeasy</td>
      <td>Diner</td>
      <td>Furniture / Home Store</td>
    </tr>
    <tr>
      <td>35</td>
      <td>West Toronto</td>
      <td>0</td>
      <td>Breakfast Spot</td>
      <td>Gift Shop</td>
      <td>Italian Restaurant</td>
      <td>Dessert Shop</td>
      <td>Eastern European Restaurant</td>
      <td>Bar</td>
      <td>Bank</td>
      <td>Restaurant</td>
      <td>Dog Run</td>
      <td>Movie Theater</td>
    </tr>
    <tr>
      <td>36</td>
      <td>West Toronto</td>
      <td>0</td>
      <td>Café</td>
      <td>Sushi Restaurant</td>
      <td>Coffee Shop</td>
      <td>Italian Restaurant</td>
      <td>Falafel Restaurant</td>
      <td>Bookstore</td>
      <td>Burrito Place</td>
      <td>Latin American Restaurant</td>
      <td>Smoothie Shop</td>
      <td>Fish &amp; Chips Shop</td>
    </tr>
    <tr>
      <td>37</td>
      <td>East Toronto</td>
      <td>0</td>
      <td>Light Rail Station</td>
      <td>Fast Food Restaurant</td>
      <td>Burrito Place</td>
      <td>Auto Workshop</td>
      <td>Spa</td>
      <td>Garden</td>
      <td>Garden Center</td>
      <td>Brewery</td>
      <td>Park</td>
      <td>Smoke Shop</td>
    </tr>
  </tbody>
</table>
</div>



#### Cluster 2


```python
Toronto_merged.loc[Toronto_merged['Cluster Labels'] == 1, Toronto_merged.columns[[1] + list(range(5, Toronto_merged.shape[1]))]]
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Borough</th>
      <th>Cluster Labels</th>
      <th>1st Most Common Venue</th>
      <th>2nd Most Common Venue</th>
      <th>3rd Most Common Venue</th>
      <th>4th Most Common Venue</th>
      <th>5th Most Common Venue</th>
      <th>6th Most Common Venue</th>
      <th>7th Most Common Venue</th>
      <th>8th Most Common Venue</th>
      <th>9th Most Common Venue</th>
      <th>10th Most Common Venue</th>
    </tr>
    <tr>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>8</td>
      <td>Central Toronto</td>
      <td>1</td>
      <td>Park</td>
      <td>Tennis Court</td>
      <td>Wings Joint</td>
      <td>Diner</td>
      <td>Fast Food Restaurant</td>
      <td>Farmers Market</td>
      <td>Falafel Restaurant</td>
      <td>Event Space</td>
      <td>Ethiopian Restaurant</td>
      <td>Electronics Store</td>
    </tr>
  </tbody>
</table>
</div>



#### Cluster 3


```python
Toronto_merged.loc[Toronto_merged['Cluster Labels'] == 2, Toronto_merged.columns[[1] + list(range(5, Toronto_merged.shape[1]))]]
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Borough</th>
      <th>Cluster Labels</th>
      <th>1st Most Common Venue</th>
      <th>2nd Most Common Venue</th>
      <th>3rd Most Common Venue</th>
      <th>4th Most Common Venue</th>
      <th>5th Most Common Venue</th>
      <th>6th Most Common Venue</th>
      <th>7th Most Common Venue</th>
      <th>8th Most Common Venue</th>
      <th>9th Most Common Venue</th>
      <th>10th Most Common Venue</th>
    </tr>
    <tr>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>22</td>
      <td>Central Toronto</td>
      <td>2</td>
      <td>Garden</td>
      <td>Home Service</td>
      <td>Pool</td>
      <td>Eastern European Restaurant</td>
      <td>Discount Store</td>
      <td>Dog Run</td>
      <td>Doner Restaurant</td>
      <td>Donut Shop</td>
      <td>Dumpling Restaurant</td>
      <td>Wings Joint</td>
    </tr>
  </tbody>
</table>
</div>



#### Cluster 4


```python
Toronto_merged.loc[Toronto_merged['Cluster Labels'] == 3, Toronto_merged.columns[[1] + list(range(5, Toronto_merged.shape[1]))]]
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Borough</th>
      <th>Cluster Labels</th>
      <th>1st Most Common Venue</th>
      <th>2nd Most Common Venue</th>
      <th>3rd Most Common Venue</th>
      <th>4th Most Common Venue</th>
      <th>5th Most Common Venue</th>
      <th>6th Most Common Venue</th>
      <th>7th Most Common Venue</th>
      <th>8th Most Common Venue</th>
      <th>9th Most Common Venue</th>
      <th>10th Most Common Venue</th>
    </tr>
    <tr>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>4</td>
      <td>Central Toronto</td>
      <td>3</td>
      <td>Park</td>
      <td>Bus Line</td>
      <td>Swim School</td>
      <td>Wings Joint</td>
      <td>Discount Store</td>
      <td>Fast Food Restaurant</td>
      <td>Farmers Market</td>
      <td>Falafel Restaurant</td>
      <td>Event Space</td>
      <td>Ethiopian Restaurant</td>
    </tr>
  </tbody>
</table>
</div>



#### Cluster 5


```python
Toronto_merged.loc[Toronto_merged['Cluster Labels'] == 4, Toronto_merged.columns[[1] + list(range(5, Toronto_merged.shape[1]))]]
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Borough</th>
      <th>Cluster Labels</th>
      <th>1st Most Common Venue</th>
      <th>2nd Most Common Venue</th>
      <th>3rd Most Common Venue</th>
      <th>4th Most Common Venue</th>
      <th>5th Most Common Venue</th>
      <th>6th Most Common Venue</th>
      <th>7th Most Common Venue</th>
      <th>8th Most Common Venue</th>
      <th>9th Most Common Venue</th>
      <th>10th Most Common Venue</th>
    </tr>
    <tr>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>10</td>
      <td>Downtown Toronto</td>
      <td>4</td>
      <td>Park</td>
      <td>Playground</td>
      <td>Trail</td>
      <td>Building</td>
      <td>Wings Joint</td>
      <td>Eastern European Restaurant</td>
      <td>Dog Run</td>
      <td>Doner Restaurant</td>
      <td>Donut Shop</td>
      <td>Dumpling Restaurant</td>
    </tr>
  </tbody>
</table>
</div>



### After seeing the information contained in the dataframes of each cluster we can suggest that:

### The number of neighborhoods in the first Cluster is due to the fact that they are very closely related to each other. For example, at least 29 of the 36 neighborhoods in that cluster have coffee shops or Coffee Shops among their common venues

### The other 4 clusters do not have many things in common

### This is the reason why the program put most of the neighborhoods in the first cluster; really the preferences are similar !!


```python

```
