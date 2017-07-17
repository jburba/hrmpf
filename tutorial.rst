UVData: Selecting data
---------
The select method lets you select specific antennas (by number or name),
antenna pairs, frequencies (in Hz or by channel number), times or polarizations
to keep in the object while removing others.

a) Select 3 antennas to keep using the antenna number.
****************
::

  from pyuvdata import UVData
  import numpy as np
  UV = UVData()
  filename = 'pyuvdata/data/day2_TDEM0003_10s_norx_1src_1spw.uvfits'
  UV.read_uvfits(filename)
  # print all the antennas numbers with data in the original file
  print(np.unique(UV.ant_1_array.tolist() + UV.ant_2_array.tolist()))
  UV.select(antenna_nums=[0, 11, 20])
  # print all the antennas numbers with data after the select
  print(np.unique(UV.ant_1_array.tolist() + UV.ant_2_array.tolist()))

b) Select 3 antennas to keep using the antenna names, also select 5 frequencies to keep.
****************
::

  from pyuvdata import UVData
  import numpy as np
  UV = UVData()
  filename = 'pyuvdata/data/day2_TDEM0003_10s_norx_1src_1spw.uvfits'
  UV.read_uvfits(filename)
  # print all the antenna names with data in the original file
  unique_ants = np.unique(UV.ant_1_array.tolist() + UV.ant_2_array.tolist())
  print([UV.antenna_names[np.where(UV.antenna_numbers==a)[0][0]] for a in unique_ants])
  # print all the frequencies in the original file
  print(UV.freq_array)
  UV.select(antenna_names=['N02', 'E09', 'W06'], frequencies=UV.freq_array[0,0:4])
  # print all the antenna names with data after the select
  unique_ants = np.unique(UV.ant_1_array.tolist() + UV.ant_2_array.tolist())
  print([UV.antenna_names[np.where(UV.antenna_numbers==a)[0][0]] for a in unique_ants])
  # print all the frequencies after the select
  print(UV.freq_array)

c) Select a few antenna pairs to keep
****************
::

  from pyuvdata import UVData
  UV = UVData()
  filename = 'pyuvdata/data/day2_TDEM0003_10s_norx_1src_1spw.uvfits'
  UV.read_uvfits(filename)
  # print all the antenna pairs with data in the original file
  print(set(zip(UV.ant_1_array, UV.ant_2_array)))
  UV.select(ant_pairs_nums=[(0, 2), (6, 0), (0, 21)])
  # note that order of the values in the pair does not matter
  # print all the antenna pairs after the select
  print(set(zip(UV.ant_1_array, UV.ant_2_array)))

d) Select antenna pairs and polarizations using ant_str argument
****************

Basic options are 'auto', 'cross', or 'all'.  The ant_str can also contain:

1. Individual antenna number(s):
____

    - 1: returns all antenna pairs containing antenna number 1 (including the auto correlation)
    - 1,2: returns all antenna pairs containing antennas 1 and 2
2. Individual baseline(s):
____

    - 1_2: returns only the antenna pair (1,2)
    - 1_2,1_3,1_10: returns antenna pairs (1,2),(1,3),(1,10)
    - (1,2)_3: returns antenna pairs (1,3),(2,3)
    - 1_(2,3): returns antenna pairs (1,2),(1,3)
3. Antenna number(s) and polarization(s):
____

when polarization information is passed with antenna numbers,
all antenna pairs kept in the object will retain data for each specified polarization
    - 1x: returns all antenna pairs containing antenna number 1 and polarizations xx and xy
    - 2x_3y: returns the antenna pair (2,3) and polarization xy
    - 1r_2l,1l_3l,1r_4r: returns antenna pairs (1,2), (1,3), (1,4) and polarizations rr, ll, and rl.
      This yields a complete list of baselines with polarizations of 1r_2l, 1l_2l, 1r_2r, 1r_3l, 1l_3l, 1r_3r, 1r_11l, 1l_11l, and 1r_11r.
    - (1x,2y)_(3x,4y): returns antenna pairs (1,3),(1,4),(2,3),(2,4) and polarizations xx, yy, xy, and yx
    - 2l_3: returns antenna pair (2,3) and polarizations ll and lr
    - 2r_3: returns antenna pair (2,3) and polarizations rr and rl
    - 1l_3,2x_3: returns antenna pairs (1,3), (2,3) and polarizations ll, lr, xx, and xy
    - 1_3l,2_3x: returns antenna pairs (1,3), (2,3) and polarizations ll, rl, xx, and yx
4. Stokes parameter(s):
____
Can be passed lower/uppercase
    - i,I: keeps only Stokes I
    - q,V: keeps both Stokes Q and V
5. Minus sign(s):
____
If a minus sign is present in front of an antenna number, it will not be kept in the data
    - 1,-3: returns all antenna pairs containing antenna 1, but removes any containing antenna 3
    - 1,-1_3: returns all antenna pairs containing antenna 1, except the antenna pair (1,3)
    - 1x_(-3y,10x): returns antenna pair (1,10) and polarization xx

::

    from pyuvdata import UVData
    import numpy as np
    UV = UVData()
    filename = 'pyuvdata/data/day2_TDEM0003_10s_norx_1src_1spw.uvfits'
    UV.read_uvfits(filename)
    # Print all the antennas numbers with data in the original file
    print(UV.get_antpairs())
    # Apply select to UV object
    UV.select(ant_str='1,-1_3')
    # Print all the antennas numbers with data after the select
    print(UV.get_antpairs())
