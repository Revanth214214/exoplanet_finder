# exoplanet_finder
Exoplanet finder based on NASA data
NASA's Kepler sattelite watched over 150,000 stars for years, every few minutes it measures some of the features of those stars like Brightness.

Stars are usually very far away when compared to planets so, when a planet completes an orbit around the sun, it passes throgh the star and when observed from the earth we usually observe that there is a dip in the brightness of the star for a short period of time. This dip is called transit.

visually:
Normal brightness
─────────────

Planet passes

─────\____/─────

Also, there is no hard and fast rule that every dip means aplanet, a dip in the brightness of a star maybe due to various reasons like:
1. The usual planet crossing a star (say small u shaped dip)
2. Two stars orbiting each other(if one passes from other it creates a dip as well)
3. Instrument noise( sometimes the telescope itself is glitching and making the weird noises on its own).

NASA records this as 'Repeating dip? could be a planet' and creates a row called TCE => 'Threshold Crossing Event'.

Now as we have the whole file of these TCE's which is our dataset, each row is a question which asks 'There is a dip in the brightness, might be a planet.'

Our job is to classify them as if they are a planet? or not.

What one row in our dataset says:
Star ID: 1234567
Found a repeating dip.
It repeats every 15 days.
The dip lasts every 4 hours.
The dip is 500 ppm deep.
Needs investigation.

All this information together is called a TCE.

We have around 150 columns in the dataset but we will look at some of the important ones.
1. kepid => Star Identifier => which star are we talking about.
2. tce_period => How frequently does the dip happen again => example: every 20 days dip happens again.
3. tce_time0bk => The first transit happened at this time.
4. tce_duration => how long the dip lasted.
5. tce_depth => how much brightness dropped => helps us tell how big the object might be.

The most important column is av_training_set, this column is like the answer key where each signal is already labbled by the scientist they observed ths ignal and labelled them. The labels are:
i) PC => Planet Candidate => looks like a planet (1)
ii) AFP => Astrophysical False Positive => Not a planet, probably another astronomical phenomenon.(example: eclipsing binary stars).(0).
iii) NTP=> Non-Transiting Phenomenon => Not a planet => Likely noise or instrumental effect (0).
iv) UNK => Unknown => Not confident enough to say(?) => we remove these here

because machine learning is supervised here we need to use examples where we know the answer but for UNK there is no trustworthy answer because it cannot be classified as a planet or any other astrophysical actitvity this might be due to lack of data, team disagreement, signal not fitting any of the existing labels.

If we keep these UNK columns and then train data we may introduce noise into our training data, hence to avoid that we keep only trusted labels.

What does the dataset not give us?
The dataset doesnt give us the actual shape of the brightness curve, it only gives us the metadata:
Period
Duration
Depth
Label

We will need the lightcurves to analyze the metadata provided, we will need the dataset to have the star id to download the MAST data using light curve.

The actual light curves come from a python library 'lightkurve' which downloads the data from NASA MAST archive using 'kepid'column from our dataset.

full pipeline:

Dataset =>Star,Period,Duration,Label => use starid to download light curve => Using period and transit time => fold the light curve=> convert into local view and global view => feed to cnn => CNN learns planet and not-planet shape => gives ouput probability of being a planet or not
          
          
          