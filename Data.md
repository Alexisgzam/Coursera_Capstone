{
 "cells": [
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "# Data "
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "The data we will use to solve our problem will be the following: \n",
    "\n",
    "1)\tDatabase with information on the State of Colima regarding the postal codes of the town. As well as latitude, longitude and cities associated with those postal codes. \n",
    "\n",
    "2)\tData obtained through the Foursquare platform referenced to the longitudes and latitudes of each postal code in the State of Colima.\n",
    "\n",
    "The first database can be found on the internet in my country's portal (https://datos.gob.mx/busca/dataset/bienes-inmuebles-del-patrimonio-del-gobierno-del-estado-de-colima-en-el-ano-2018). It is public and free information.\n",
    "\n",
    "Once we have cleared the database, we can obtain a direct relationship between postal codes, city, latitude and longitude coordinates, and with that information we will use the Foursquare platform to obtain the most popular places in a radius of 10,000 meters (remember that we are talking about cities in a complete state).\n",
    "\n",
    "Once the most popular places are obtained, we will simply apply the Clusterign-K-Means method to segment our cities and be able to visualize the relationship between the places and the groups that are created in each cluster. \n",
    "\n",
    "Additional information to consider that will be properly included in the report:\n",
    "•\tColima is one of the smallest states in my country (Mexico). \n",
    "\n",
    "•\tIts cities, although rich in culture, are very small. So an analysis of a single city would not make much sense since the information obtained can be very poor."
   ]
  }
 ],
 "metadata": {
  "kernelspec": {
   "display_name": "Python",
   "language": "python",
   "name": "conda-env-python-py"
  },
  "language_info": {
   "codemirror_mode": {
    "name": "ipython",
    "version": 3
   },
   "file_extension": ".py",
   "mimetype": "text/x-python",
   "name": "python",
   "nbconvert_exporter": "python",
   "pygments_lexer": "ipython3",
   "version": "3.6.7"
  }
 },
 "nbformat": 4,
 "nbformat_minor": 4
}
