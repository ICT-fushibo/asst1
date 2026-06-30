serial: 354.237ms

2 thread: 182.603ms 1.94x

3 thread: 215.912ms 1.68x

4thread: 150.434ms 2.40x

5 :142.753ms 2.47x

6: 111.586ms 3.17x

7: 109.324ms 3.31x

8: 90.982ms 3.92x

16: 51.354ms 6.87x

32: 32.668ms 10.85x

非线性的加速，理论上加速比应随着线程线性增长，但是增长的缓慢，并且三线程出现了性能不如2线程的情况。

推测：线程的创建和规约开销增加了整体的执行负担，在每个线程的执行中测试时间。

测试结果

t=2

[mandelbrot thread]:            [196.090] ms pre thread
[mandelbrot thread]:            [196.656] ms
[mandelbrot thread]:            [191.675] ms pre thread
[mandelbrot thread]:            [191.963] ms
[mandelbrot thread]:            [186.995] ms pre thread
[mandelbrot thread]:            [187.659] ms
[mandelbrot thread]:            [193.273] ms pre thread
[mandelbrot thread]:            [193.684] ms
[mandelbrot thread]:            [179.951] ms pre thread
[mandelbrot thread]:            [181.025] ms


t=3

[mandelbrot thread]:            [230.774] ms pre thread
[mandelbrot thread]:            [231.242] ms
[mandelbrot thread]:            [232.024] ms pre thread
[mandelbrot thread]:            [232.448] ms
[mandelbrot thread]:            [233.452] ms pre thread
[mandelbrot thread]:            [233.754] ms
[mandelbrot thread]:            [231.386] ms pre thread
[mandelbrot thread]:            [231.672] ms
[mandelbrot thread]:            [225.838] ms pre thread
[mandelbrot thread]:            [226.197] ms


t=4

[mandelbrot thread]:            [157.792] ms pre thread
[mandelbrot thread]:            [158.535] ms
[mandelbrot thread]:            [154.510] ms pre thread
[mandelbrot thread]:            [158.487] ms
[mandelbrot thread]:            [155.760] ms pre thread
[mandelbrot thread]:            [159.136] ms
[mandelbrot thread]:            [158.427] ms pre thread
[mandelbrot thread]:            [158.691] ms
[mandelbrot thread]:            [156.762] ms pre thread
[mandelbrot thread]:            [157.127] ms


t=5

[mandelbrot thread]:            [101.324] ms pre thread
[mandelbrot thread]:            [151.911] ms
[mandelbrot thread]:            [94.718] ms pre thread
[mandelbrot thread]:            [151.663] ms
[mandelbrot thread]:            [101.749] ms pre thread
[mandelbrot thread]:            [149.361] ms
[mandelbrot thread]:            [96.170] ms pre thread
[mandelbrot thread]:            [149.236] ms
[mandelbrot thread]:            [93.810] ms pre thread
[mandelbrot thread]:            [143.826] ms


t=6

[mandelbrot thread]:            [66.453] ms pre thread
[mandelbrot thread]:            [114.825] ms
[mandelbrot thread]:            [65.893] ms pre thread
[mandelbrot thread]:            [122.032] ms
[mandelbrot thread]:            [65.352] ms pre thread
[mandelbrot thread]:            [119.695] ms
[mandelbrot thread]:            [64.519] ms pre thread
[mandelbrot thread]:            [115.618] ms
[mandelbrot thread]:            [66.506] ms pre thread
[mandelbrot thread]:            [120.479] ms


t=7

[mandelbrot thread]:            [44.631] ms pre thread
[mandelbrot thread]:            [113.781] ms
[mandelbrot thread]:            [46.078] ms pre thread
[mandelbrot thread]:            [112.058] ms
[mandelbrot thread]:            [44.464] ms pre thread
[mandelbrot thread]:            [116.619] ms
[mandelbrot thread]:            [45.383] ms pre thread
[mandelbrot thread]:            [114.583] ms
[mandelbrot thread]:            [48.503] ms pre thread
[mandelbrot thread]:            [121.471] ms


t=8

[mandelbrot thread]:            [33.140] ms pre thread
[mandelbrot thread]:            [98.501] ms
[mandelbrot thread]:            [33.799] ms pre thread
[mandelbrot thread]:            [101.804] ms
[mandelbrot thread]:            [34.050] ms pre thread
[mandelbrot thread]:            [96.969] ms
[mandelbrot thread]:            [31.182] ms pre thread
[mandelbrot thread]:            [93.736] ms
[mandelbrot thread]:            [32.748] ms pre thread
[mandelbrot thread]:            [98.885] ms


t=16

[mandelbrot thread]:            [4.691] ms pre thread
[mandelbrot thread]:            [55.271] ms
[mandelbrot thread]:            [5.007] ms pre thread
[mandelbrot thread]:            [62.571] ms
[mandelbrot thread]:            [5.174] ms pre thread
[mandelbrot thread]:            [64.189] ms
[mandelbrot thread]:            [5.120] ms pre thread
[mandelbrot thread]:            [57.428] ms
[mandelbrot thread]:            [5.414] ms pre thread
[mandelbrot thread]:            [61.853] ms


t=32

[mandelbrot thread]:            [0.522] ms pre thread
[mandelbrot thread]:            [29.417] ms
[mandelbrot thread]:            [0.512] ms pre thread
[mandelbrot thread]:            [34.779] ms
[mandelbrot thread]:            [0.608] ms pre thread
[mandelbrot thread]:            [33.495] ms
[mandelbrot thread]:            [0.794] ms pre thread
[mandelbrot thread]:            [37.455] ms
[mandelbrot thread]:            [0.677] ms pre thread
[mandelbrot thread]:            [37.251] ms


确实存在线程创建-规约开销的问题，但是pre thread并没有按照预想的那样与线程数成正比，而是一条诡异的前期很慢但是后期巨快的曲线。


原因：统计线程时间时，只统计了thread=1的线程，而线程之间存在运算负载不均衡的情况，还存在等待最慢线程的情况，这也解释了在行分块逻辑下为什么thread=3反而不如thread=2的加速，因为thread1基本负责了整张图全部的运算开销。现在统计全部线程时间，在3、4、5、8、16、32线程上

t=3

[mandelbrot thread 0]:          [75.602] ms
[mandelbrot thread 2]:          [76.038] ms
[mandelbrot thread 1]:          [229.654] ms
[mandelbrot thread]:            [230.145] ms
[mandelbrot thread 2]:          [79.623] ms
[mandelbrot thread 0]:          [84.470] ms
[mandelbrot thread 1]:          [243.668] ms
[mandelbrot thread]:            [244.214] ms
[mandelbrot thread 2]:          [74.315] ms
[mandelbrot thread 0]:          [75.555] ms
[mandelbrot thread 1]:          [230.061] ms
[mandelbrot thread]:            [230.398] ms
[mandelbrot thread 0]:          [74.263] ms
[mandelbrot thread 2]:          [78.305] ms
[mandelbrot thread 1]:          [227.859] ms
[mandelbrot thread]:            [228.165] ms
[mandelbrot thread 0]:          [74.740] ms
[mandelbrot thread 2]:          [76.203] ms
[mandelbrot thread 1]:          [232.779] ms
[mandelbrot thread]:            [233.036] ms

t=4

[mandelbrot thread 0]:          [37.681] ms
[mandelbrot thread 3]:          [38.895] ms
[mandelbrot thread 2]:          [150.731] ms
[mandelbrot thread 1]:          [154.232] ms
[mandelbrot thread]:            [154.557] ms
[mandelbrot thread 0]:          [35.705] ms
[mandelbrot thread 3]:          [37.099] ms
[mandelbrot thread 1]:          [149.825] ms
[mandelbrot thread 2]:          [152.846] ms
[mandelbrot thread]:            [153.129] ms
[mandelbrot thread 0]:          [36.225] ms
[mandelbrot thread 3]:          [36.606] ms
[mandelbrot thread 1]:          [151.411] ms
[mandelbrot thread 2]:          [153.371] ms
[mandelbrot thread]:            [153.646] ms
[mandelbrot thread 0]:          [35.622] ms
[mandelbrot thread 3]:          [38.021] ms
[mandelbrot thread 1]:          [153.142] ms
[mandelbrot thread 2]:          [157.267] ms
[mandelbrot thread]:            [157.594] ms
[mandelbrot thread 3]:          [37.882] ms
[mandelbrot thread 0]:          [38.443] ms
[mandelbrot thread 1]:          [158.643] ms
[mandelbrot thread 2]:          [161.442] ms
[mandelbrot thread]:            [161.758] ms

t=5

[mandelbrot thread 4]:          [17.193] ms
[mandelbrot thread 0]:          [17.230] ms
[mandelbrot thread 1]:          [97.644] ms
[mandelbrot thread 3]:          [100.474] ms
[mandelbrot thread 2]:          [157.730] ms
[mandelbrot thread]:            [158.244] ms
[mandelbrot thread 0]:          [16.403] ms
[mandelbrot thread 4]:          [18.033] ms
[mandelbrot thread 3]:          [97.203] ms
[mandelbrot thread 1]:          [99.895] ms
[mandelbrot thread 2]:          [151.100] ms
[mandelbrot thread]:            [151.499] ms
[mandelbrot thread 0]:          [17.171] ms
[mandelbrot thread 4]:          [17.311] ms
[mandelbrot thread 1]:          [96.785] ms
[mandelbrot thread 3]:          [97.827] ms
[mandelbrot thread 2]:          [152.701] ms
[mandelbrot thread]:            [153.008] ms
[mandelbrot thread 0]:          [18.811] ms
[mandelbrot thread 4]:          [19.503] ms
[mandelbrot thread 1]:          [103.416] ms
[mandelbrot thread 3]:          [103.681] ms
[mandelbrot thread 2]:          [157.787] ms
[mandelbrot thread]:            [158.157] ms
[mandelbrot thread 0]:          [17.536] ms
[mandelbrot thread 4]:          [18.540] ms
[mandelbrot thread 1]:          [105.544] ms
[mandelbrot thread 3]:          [106.364] ms
[mandelbrot thread 2]:          [159.677] ms
[mandelbrot thread]:            [160.066] ms

t=8

[mandelbrot thread 7]:          [6.522] ms
[mandelbrot thread 0]:          [8.552] ms
[mandelbrot thread 1]:          [30.688] ms
[mandelbrot thread 6]:          [30.600] ms
[mandelbrot thread 5]:          [63.631] ms
[mandelbrot thread 2]:          [64.264] ms
[mandelbrot thread 4]:          [94.348] ms
[mandelbrot thread 3]:          [95.887] ms
[mandelbrot thread]:            [96.483] ms
[mandelbrot thread 0]:          [6.170] ms
[mandelbrot thread 7]:          [7.089] ms
[mandelbrot thread 6]:          [31.869] ms
[mandelbrot thread 1]:          [35.111] ms
[mandelbrot thread 2]:          [63.992] ms
[mandelbrot thread 5]:          [65.152] ms
[mandelbrot thread 4]:          [95.340] ms
[mandelbrot thread 3]:          [99.266] ms
[mandelbrot thread]:            [99.915] ms
[mandelbrot thread 0]:          [5.986] ms
[mandelbrot thread 7]:          [6.004] ms
[mandelbrot thread 1]:          [37.799] ms
[mandelbrot thread 6]:          [38.516] ms
[mandelbrot thread 5]:          [74.063] ms
[mandelbrot thread 2]:          [77.733] ms
[mandelbrot thread 4]:          [104.999] ms
[mandelbrot thread 3]:          [108.340] ms
[mandelbrot thread]:            [108.935] ms
[mandelbrot thread 7]:          [6.035] ms
[mandelbrot thread 0]:          [6.704] ms
[mandelbrot thread 1]:          [32.167] ms
[mandelbrot thread 6]:          [35.014] ms
[mandelbrot thread 5]:          [64.292] ms
[mandelbrot thread 2]:          [65.221] ms
[mandelbrot thread 4]:          [94.530] ms
[mandelbrot thread 3]:          [96.595] ms
[mandelbrot thread]:            [97.295] ms
[mandelbrot thread 0]:          [5.726] ms
[mandelbrot thread 7]:          [5.820] ms
[mandelbrot thread 1]:          [31.413] ms
[mandelbrot thread 6]:          [32.470] ms
[mandelbrot thread 2]:          [63.984] ms
[mandelbrot thread 5]:          [64.402] ms
[mandelbrot thread 3]:          [96.188] ms
[mandelbrot thread 4]:          [97.592] ms
[mandelbrot thread]:            [98.103] ms

t=16

[mandelbrot thread 0]:          [0.998] ms
[mandelbrot thread 15]:         [1.045] ms
[mandelbrot thread 1]:          [5.080] ms
[mandelbrot thread 14]:         [5.789] ms
[mandelbrot thread 2]:          [8.364] ms
[mandelbrot thread 13]:         [8.484] ms
[mandelbrot thread 3]:          [28.020] ms
[mandelbrot thread 12]:         [33.886] ms
[mandelbrot thread 4]:          [37.067] ms
[mandelbrot thread 11]:         [38.411] ms
[mandelbrot thread 10]:         [43.598] ms
[mandelbrot thread 5]:          [47.476] ms
[mandelbrot thread 9]:          [58.818] ms
[mandelbrot thread 8]:          [61.216] ms
[mandelbrot thread 7]:          [61.999] ms
[mandelbrot thread 6]:          [65.622] ms
[mandelbrot thread]:            [66.632] ms
[mandelbrot thread 0]:          [1.335] ms
[mandelbrot thread 15]:         [1.680] ms
[mandelbrot thread 1]:          [5.610] ms
[mandelbrot thread 14]:         [6.004] ms
[mandelbrot thread 13]:         [8.320] ms
[mandelbrot thread 2]:          [10.560] ms
[mandelbrot thread 12]:         [27.844] ms
[mandelbrot thread 3]:          [30.344] ms
[mandelbrot thread 11]:         [37.282] ms
[mandelbrot thread 4]:          [39.188] ms
[mandelbrot thread 10]:         [42.632] ms
[mandelbrot thread 5]:          [43.820] ms
[mandelbrot thread 6]:          [57.009] ms
[mandelbrot thread 9]:          [60.918] ms
[mandelbrot thread 8]:          [62.049] ms
[mandelbrot thread 7]:          [68.034] ms
[mandelbrot thread]:            [69.233] ms
[mandelbrot thread 0]:          [1.061] ms
[mandelbrot thread 15]:         [1.075] ms
[mandelbrot thread 1]:          [5.033] ms
[mandelbrot thread 14]:         [5.298] ms
[mandelbrot thread 2]:          [9.939] ms
[mandelbrot thread 13]:         [11.469] ms
[mandelbrot thread 3]:          [26.390] ms
[mandelbrot thread 12]:         [25.591] ms
[mandelbrot thread 4]:          [30.724] ms
[mandelbrot thread 11]:         [31.819] ms
[mandelbrot thread 10]:         [38.712] ms
[mandelbrot thread 5]:          [39.800] ms
[mandelbrot thread 9]:          [50.873] ms
[mandelbrot thread 6]:          [53.326] ms
[mandelbrot thread 8]:          [55.840] ms
[mandelbrot thread 7]:          [57.810] ms
[mandelbrot thread]:            [59.066] ms
[mandelbrot thread 0]:          [1.184] ms
[mandelbrot thread 15]:         [1.724] ms
[mandelbrot thread 1]:          [5.181] ms
[mandelbrot thread 2]:          [7.837] ms
[mandelbrot thread 13]:         [8.226] ms
[mandelbrot thread 14]:         [11.372] ms
[mandelbrot thread 3]:          [27.941] ms
[mandelbrot thread 12]:         [34.527] ms
[mandelbrot thread 4]:          [37.653] ms
[mandelbrot thread 10]:         [42.914] ms
[mandelbrot thread 5]:          [47.341] ms
[mandelbrot thread 11]:         [50.175] ms
[mandelbrot thread 6]:          [51.116] ms
[mandelbrot thread 9]:          [55.189] ms
[mandelbrot thread 7]:          [58.889] ms
[mandelbrot thread 8]:          [67.506] ms
[mandelbrot thread]:            [68.897] ms
[mandelbrot thread 15]:         [1.071] ms
[mandelbrot thread 0]:          [1.581] ms
[mandelbrot thread 1]:          [5.755] ms
[mandelbrot thread 14]:         [5.732] ms
[mandelbrot thread 2]:          [8.285] ms
[mandelbrot thread 13]:         [7.890] ms
[mandelbrot thread 3]:          [29.546] ms
[mandelbrot thread 12]:         [31.113] ms
[mandelbrot thread 11]:         [37.876] ms
[mandelbrot thread 4]:          [38.901] ms
[mandelbrot thread 5]:          [41.255] ms
[mandelbrot thread 10]:         [45.855] ms
[mandelbrot thread 6]:          [55.977] ms
[mandelbrot thread 9]:          [58.800] ms
[mandelbrot thread 8]:          [60.705] ms
[mandelbrot thread 7]:          [62.222] ms
[mandelbrot thread]:            [63.219] ms

t=32

[mandelbrot thread 1]:          [0.501] ms
[mandelbrot thread 2]:          [1.908] ms
[mandelbrot thread 4]:          [3.273] ms
[mandelbrot thread 31]:         [0.471] ms
[mandelbrot thread 3]:          [3.480] ms
[mandelbrot thread 0]:          [0.818] ms
[mandelbrot thread 30]:         [0.798] ms
[mandelbrot thread 29]:         [1.833] ms
[mandelbrot thread 5]:          [4.466] ms
[mandelbrot thread 28]:         [3.158] ms
[mandelbrot thread 26]:         [4.075] ms
[mandelbrot thread 27]:         [3.845] ms
[mandelbrot thread 6]:          [9.849] ms
[mandelbrot thread 7]:          [12.542] ms
[mandelbrot thread 25]:         [10.935] ms
[mandelbrot thread 23]:         [13.632] ms
[mandelbrot thread 8]:          [15.840] ms
[mandelbrot thread 9]:          [16.421] ms
[mandelbrot thread 10]:         [18.625] ms
[mandelbrot thread 24]:         [19.106] ms
[mandelbrot thread 21]:         [20.635] ms
[mandelbrot thread 22]:         [20.655] ms
[mandelbrot thread 20]:         [22.030] ms
[mandelbrot thread 11]:         [23.303] ms
[mandelbrot thread 12]:         [23.209] ms
[mandelbrot thread 19]:         [24.676] ms
[mandelbrot thread 14]:         [25.508] ms
[mandelbrot thread 18]:         [26.426] ms
[mandelbrot thread 13]:         [28.056] ms
[mandelbrot thread 17]:         [28.459] ms
[mandelbrot thread 16]:         [29.448] ms
[mandelbrot thread 15]:         [31.778] ms
[mandelbrot thread]:            [33.933] ms
[mandelbrot thread 1]:          [0.594] ms
[mandelbrot thread 2]:          [1.721] ms
[mandelbrot thread 3]:          [3.666] ms
[mandelbrot thread 4]:          [3.485] ms
[mandelbrot thread 30]:         [0.616] ms
[mandelbrot thread 0]:          [0.652] ms
[mandelbrot thread 31]:         [0.689] ms
[mandelbrot thread 29]:         [1.880] ms
[mandelbrot thread 27]:         [3.806] ms
[mandelbrot thread 28]:         [3.735] ms
[mandelbrot thread 5]:          [6.805] ms
[mandelbrot thread 26]:         [6.076] ms
[mandelbrot thread 6]:          [9.990] ms
[mandelbrot thread 25]:         [12.218] ms
[mandelbrot thread 23]:         [13.974] ms
[mandelbrot thread 8]:          [16.011] ms
[mandelbrot thread 7]:          [17.635] ms
[mandelbrot thread 22]:         [15.949] ms
[mandelbrot thread 9]:          [18.609] ms
[mandelbrot thread 24]:         [19.287] ms
[mandelbrot thread 10]:         [22.138] ms
[mandelbrot thread 20]:         [21.262] ms
[mandelbrot thread 11]:         [22.704] ms
[mandelbrot thread 21]:         [22.343] ms
[mandelbrot thread 12]:         [26.017] ms
[mandelbrot thread 13]:         [30.127] ms
[mandelbrot thread 18]:         [30.596] ms
[mandelbrot thread 19]:         [31.019] ms
[mandelbrot thread 14]:         [32.987] ms
[mandelbrot thread 16]:         [34.220] ms
[mandelbrot thread 17]:         [34.411] ms
[mandelbrot thread 15]:         [38.217] ms
[mandelbrot thread]:            [41.350] ms
[mandelbrot thread 1]:          [0.667] ms
[mandelbrot thread 2]:          [1.812] ms
[mandelbrot thread 4]:          [3.664] ms
[mandelbrot thread 0]:          [0.477] ms
[mandelbrot thread 3]:          [4.634] ms
[mandelbrot thread 5]:          [4.398] ms
[mandelbrot thread 30]:         [0.842] ms
[mandelbrot thread 31]:         [0.680] ms
[mandelbrot thread 29]:         [2.173] ms
[mandelbrot thread 28]:         [3.377] ms
[mandelbrot thread 27]:         [3.914] ms
[mandelbrot thread 26]:         [4.756] ms
[mandelbrot thread 24]:         [15.692] ms
[mandelbrot thread 8]:          [18.915] ms
[mandelbrot thread 25]:         [16.527] ms
[mandelbrot thread 6]:          [19.449] ms
[mandelbrot thread 22]:         [19.405] ms
[mandelbrot thread 9]:          [21.630] ms
[mandelbrot thread 23]:         [20.989] ms
[mandelbrot thread 7]:          [24.448] ms
[mandelbrot thread 10]:         [24.842] ms
[mandelbrot thread 11]:         [25.580] ms
[mandelbrot thread 20]:         [26.900] ms
[mandelbrot thread 12]:         [30.780] ms
[mandelbrot thread 21]:         [31.586] ms
[mandelbrot thread 13]:         [32.654] ms
[mandelbrot thread 19]:         [32.156] ms
[mandelbrot thread 14]:         [34.210] ms
[mandelbrot thread 15]:         [35.305] ms
[mandelbrot thread 18]:         [35.334] ms
[mandelbrot thread 16]:         [37.575] ms
[mandelbrot thread 17]:         [37.603] ms
[mandelbrot thread]:            [40.463] ms
[mandelbrot thread 1]:          [0.624] ms
[mandelbrot thread 2]:          [1.672] ms
[mandelbrot thread 3]:          [3.627] ms
[mandelbrot thread 31]:         [0.652] ms
[mandelbrot thread 0]:          [0.672] ms
[mandelbrot thread 30]:         [0.835] ms
[mandelbrot thread 4]:          [3.831] ms
[mandelbrot thread 29]:         [2.192] ms
[mandelbrot thread 5]:          [4.916] ms
[mandelbrot thread 28]:         [3.689] ms
[mandelbrot thread 27]:         [4.056] ms
[mandelbrot thread 26]:         [4.894] ms
[mandelbrot thread 6]:          [13.413] ms
[mandelbrot thread 25]:         [17.595] ms
[mandelbrot thread 9]:          [20.338] ms
[mandelbrot thread 7]:          [20.610] ms
[mandelbrot thread 8]:          [21.045] ms
[mandelbrot thread 23]:         [21.022] ms
[mandelbrot thread 10]:         [23.821] ms
[mandelbrot thread 21]:         [23.252] ms
[mandelbrot thread 20]:         [23.526] ms
[mandelbrot thread 24]:         [21.481] ms
[mandelbrot thread 22]:         [23.595] ms
[mandelbrot thread 13]:         [29.896] ms
[mandelbrot thread 12]:         [30.639] ms
[mandelbrot thread 11]:         [33.140] ms
[mandelbrot thread 14]:         [34.111] ms
[mandelbrot thread 18]:         [34.191] ms
[mandelbrot thread 19]:         [35.218] ms
[mandelbrot thread 15]:         [37.523] ms
[mandelbrot thread 17]:         [40.529] ms
[mandelbrot thread 16]:         [40.999] ms
[mandelbrot thread]:            [43.338] ms
[mandelbrot thread 1]:          [0.731] ms
[mandelbrot thread 2]:          [1.856] ms
[mandelbrot thread 3]:          [3.711] ms
[mandelbrot thread 4]:          [3.754] ms
[mandelbrot thread 0]:          [0.668] ms
[mandelbrot thread 31]:         [0.689] ms
[mandelbrot thread 30]:         [0.882] ms
[mandelbrot thread 5]:          [4.735] ms
[mandelbrot thread 29]:         [2.465] ms
[mandelbrot thread 27]:         [3.923] ms
[mandelbrot thread 28]:         [3.768] ms
[mandelbrot thread 26]:         [4.753] ms
[mandelbrot thread 6]:          [10.486] ms
[mandelbrot thread 25]:         [10.945] ms
[mandelbrot thread 8]:          [14.192] ms
[mandelbrot thread 7]:          [15.321] ms
[mandelbrot thread 24]:         [15.773] ms
[mandelbrot thread 9]:          [17.894] ms
[mandelbrot thread 22]:         [17.844] ms
[mandelbrot thread 10]:         [20.473] ms
[mandelbrot thread 23]:         [19.478] ms
[mandelbrot thread 11]:         [21.527] ms
[mandelbrot thread 20]:         [21.489] ms
[mandelbrot thread 21]:         [22.402] ms
[mandelbrot thread 12]:         [27.052] ms
[mandelbrot thread 19]:         [27.088] ms
[mandelbrot thread 13]:         [27.961] ms
[mandelbrot thread 18]:         [27.905] ms
[mandelbrot thread 17]:         [29.150] ms
[mandelbrot thread 16]:         [29.859] ms
[mandelbrot thread 15]:         [31.707] ms
[mandelbrot thread 14]:         [34.136] ms
[mandelbrot thread]:            [36.483] ms


实验结果印证了这一猜想。后续优化：线程负载均衡，每个线程从全部行中均匀抽样。测试t=3、5、8、16、32

t=3

[mandelbrot serial]:            [358.406] ms
Wrote image file mandelbrot-serial.ppm
[mandelbrot thread 1]:          [126.712] ms
[mandelbrot thread 2]:          [128.214] ms
[mandelbrot thread 0]:          [131.841] ms
[mandelbrot thread]:            [132.091] ms
[mandelbrot thread 1]:          [128.015] ms
[mandelbrot thread 2]:          [128.054] ms
[mandelbrot thread 0]:          [134.488] ms
[mandelbrot thread]:            [134.717] ms
[mandelbrot thread 2]:          [126.755] ms
[mandelbrot thread 1]:          [128.834] ms
[mandelbrot thread 0]:          [129.865] ms
[mandelbrot thread]:            [130.184] ms
[mandelbrot thread 1]:          [125.352] ms
[mandelbrot thread 2]:          [127.700] ms
[mandelbrot thread 0]:          [131.572] ms
[mandelbrot thread]:            [131.807] ms
[mandelbrot thread 1]:          [126.986] ms
[mandelbrot thread 2]:          [127.086] ms
[mandelbrot thread 0]:          [127.383] ms
[mandelbrot thread]:            [127.620] ms
[mandelbrot thread BEST]:               [127.620] ms
Wrote image file mandelbrot-thread.ppm
                                (2.81x speedup from 3 threads)

t=5

[mandelbrot serial]:            [359.127] ms
Wrote image file mandelbrot-serial.ppm
[mandelbrot thread 2]:          [75.911] ms
[mandelbrot thread 3]:          [77.586] ms
[mandelbrot thread 1]:          [78.050] ms
[mandelbrot thread 0]:          [79.069] ms
[mandelbrot thread 4]:          [79.350] ms
[mandelbrot thread]:            [79.867] ms
[mandelbrot thread 3]:          [78.593] ms
[mandelbrot thread 0]:          [78.765] ms
[mandelbrot thread 1]:          [79.630] ms
[mandelbrot thread 2]:          [80.055] ms
[mandelbrot thread 4]:          [81.126] ms
[mandelbrot thread]:            [81.604] ms
[mandelbrot thread 2]:          [79.712] ms
[mandelbrot thread 4]:          [80.498] ms
[mandelbrot thread 1]:          [81.627] ms
[mandelbrot thread 3]:          [82.581] ms
[mandelbrot thread 0]:          [82.828] ms
[mandelbrot thread]:            [83.385] ms
[mandelbrot thread 2]:          [77.652] ms
[mandelbrot thread 3]:          [77.816] ms
[mandelbrot thread 1]:          [78.062] ms
[mandelbrot thread 0]:          [78.172] ms
[mandelbrot thread 4]:          [79.070] ms
[mandelbrot thread]:            [79.647] ms
[mandelbrot thread 2]:          [77.089] ms
[mandelbrot thread 1]:          [77.198] ms
[mandelbrot thread 4]:          [77.193] ms
[mandelbrot thread 3]:          [77.630] ms
[mandelbrot thread 0]:          [80.823] ms
[mandelbrot thread]:            [81.248] ms
[mandelbrot thread BEST]:               [79.647] ms
Wrote image file mandelbrot-thread.ppm
                                (4.51x speedup from 5 threads)

t=8

[mandelbrot serial]:            [359.833] ms
Wrote image file mandelbrot-serial.ppm
[mandelbrot thread 5]:          [49.004] ms
[mandelbrot thread 6]:          [49.314] ms
[mandelbrot thread 3]:          [49.633] ms
[mandelbrot thread 2]:          [50.269] ms
[mandelbrot thread 1]:          [50.364] ms
[mandelbrot thread 7]:          [50.282] ms
[mandelbrot thread 4]:          [51.000] ms
[mandelbrot thread 0]:          [52.049] ms
[mandelbrot thread]:            [52.998] ms
[mandelbrot thread 7]:          [51.081] ms
[mandelbrot thread 2]:          [52.663] ms
[mandelbrot thread 3]:          [52.633] ms
[mandelbrot thread 5]:          [52.853] ms
[mandelbrot thread 4]:          [53.421] ms
[mandelbrot thread 0]:          [53.391] ms
[mandelbrot thread 6]:          [53.531] ms
[mandelbrot thread 1]:          [55.664] ms
[mandelbrot thread]:            [55.958] ms
[mandelbrot thread 4]:          [51.840] ms
[mandelbrot thread 6]:          [52.177] ms
[mandelbrot thread 2]:          [53.278] ms
[mandelbrot thread 7]:          [53.081] ms
[mandelbrot thread 1]:          [54.204] ms
[mandelbrot thread 0]:          [54.965] ms
[mandelbrot thread 3]:          [56.706] ms
[mandelbrot thread 5]:          [56.578] ms
[mandelbrot thread]:            [57.372] ms
[mandelbrot thread 7]:          [50.906] ms
[mandelbrot thread 3]:          [51.921] ms
[mandelbrot thread 4]:          [52.343] ms
[mandelbrot thread 6]:          [53.173] ms
[mandelbrot thread 5]:          [53.597] ms
[mandelbrot thread 0]:          [53.448] ms
[mandelbrot thread 2]:          [54.793] ms
[mandelbrot thread 1]:          [55.471] ms
[mandelbrot thread]:            [55.748] ms
[mandelbrot thread 5]:          [57.068] ms
[mandelbrot thread 4]:          [57.626] ms
[mandelbrot thread 3]:          [58.144] ms
[mandelbrot thread 6]:          [58.279] ms
[mandelbrot thread 1]:          [58.929] ms
[mandelbrot thread 7]:          [58.603] ms
[mandelbrot thread 2]:          [59.857] ms
[mandelbrot thread 0]:          [59.545] ms
[mandelbrot thread]:            [60.387] ms
[mandelbrot thread BEST]:               [52.998] ms
Wrote image file mandelbrot-thread.ppm
                                (6.79x speedup from 8 threads)

t=16

[mandelbrot serial]:            [357.218] ms
Wrote image file mandelbrot-serial.ppm
[mandelbrot thread 9]:          [25.031] ms
[mandelbrot thread 6]:          [26.710] ms
[mandelbrot thread 3]:          [27.023] ms
[mandelbrot thread 4]:          [27.630] ms
[mandelbrot thread 13]:         [25.813] ms
[mandelbrot thread 2]:          [28.333] ms
[mandelbrot thread 12]:         [26.896] ms
[mandelbrot thread 14]:         [26.331] ms
[mandelbrot thread 1]:          [29.124] ms
[mandelbrot thread 11]:         [28.490] ms
[mandelbrot thread 7]:          [29.592] ms
[mandelbrot thread 10]:         [29.339] ms
[mandelbrot thread 5]:          [30.222] ms
[mandelbrot thread 8]:          [31.895] ms
[mandelbrot thread 15]:         [31.820] ms
[mandelbrot thread 0]:          [35.494] ms
[mandelbrot thread]:            [39.068] ms
[mandelbrot thread 1]:          [26.312] ms
[mandelbrot thread 4]:          [27.110] ms
[mandelbrot thread 2]:          [27.567] ms
[mandelbrot thread 9]:          [26.423] ms
[mandelbrot thread 7]:          [27.223] ms
[mandelbrot thread 8]:          [26.724] ms
[mandelbrot thread 10]:         [26.817] ms
[mandelbrot thread 11]:         [26.469] ms
[mandelbrot thread 6]:          [28.257] ms
[mandelbrot thread 12]:         [27.120] ms
[mandelbrot thread 14]:         [27.028] ms
[mandelbrot thread 13]:         [27.763] ms
[mandelbrot thread 5]:          [32.323] ms
[mandelbrot thread 0]:          [29.261] ms
[mandelbrot thread 15]:         [29.314] ms
[mandelbrot thread 3]:          [33.542] ms
[mandelbrot thread]:            [34.488] ms
[mandelbrot thread 2]:          [29.422] ms
[mandelbrot thread 6]:          [29.565] ms
[mandelbrot thread 3]:          [30.560] ms
[mandelbrot thread 10]:         [29.322] ms
[mandelbrot thread 1]:          [33.053] ms
[mandelbrot thread 7]:          [31.766] ms
[mandelbrot thread 12]:         [31.876] ms
[mandelbrot thread 5]:          [33.917] ms
[mandelbrot thread 11]:         [33.561] ms
[mandelbrot thread 9]:          [34.145] ms
[mandelbrot thread 14]:         [34.160] ms
[mandelbrot thread 8]:          [35.928] ms
[mandelbrot thread 13]:         [35.686] ms
[mandelbrot thread 4]:          [38.145] ms
[mandelbrot thread 15]:         [36.603] ms
[mandelbrot thread 0]:          [40.663] ms
[mandelbrot thread]:            [43.133] ms
[mandelbrot thread 13]:         [28.251] ms
[mandelbrot thread 14]:         [28.882] ms
[mandelbrot thread 2]:          [31.413] ms
[mandelbrot thread 10]:         [30.725] ms
[mandelbrot thread 3]:          [31.929] ms
[mandelbrot thread 7]:          [31.207] ms
[mandelbrot thread 9]:          [31.999] ms
[mandelbrot thread 1]:          [32.901] ms
[mandelbrot thread 8]:          [32.611] ms
[mandelbrot thread 11]:         [33.020] ms
[mandelbrot thread 6]:          [34.398] ms
[mandelbrot thread 4]:          [36.265] ms
[mandelbrot thread 12]:         [35.489] ms
[mandelbrot thread 5]:          [36.944] ms
[mandelbrot thread 15]:         [37.305] ms
[mandelbrot thread 0]:          [38.302] ms
[mandelbrot thread]:            [40.083] ms
[mandelbrot thread 11]:         [28.497] ms
[mandelbrot thread 6]:          [29.324] ms
[mandelbrot thread 13]:         [29.191] ms
[mandelbrot thread 14]:         [29.441] ms
[mandelbrot thread 12]:         [29.685] ms
[mandelbrot thread 9]:          [30.687] ms
[mandelbrot thread 7]:          [31.108] ms
[mandelbrot thread 2]:          [31.856] ms
[mandelbrot thread 15]:         [30.470] ms
[mandelbrot thread 5]:          [31.588] ms
[mandelbrot thread 10]:         [32.206] ms
[mandelbrot thread 8]:          [32.575] ms
[mandelbrot thread 3]:          [33.468] ms
[mandelbrot thread 1]:          [33.837] ms
[mandelbrot thread 0]:          [33.619] ms
[mandelbrot thread 4]:          [35.874] ms
[mandelbrot thread]:            [36.552] ms
[mandelbrot thread BEST]:               [34.488] ms
Wrote image file mandelbrot-thread.ppm
                                (10.36x speedup from 16 threads)

t=32

[mandelbrot serial]:            [363.192] ms
Wrote image file mandelbrot-serial.ppm
[mandelbrot thread 1]:          [11.650] ms
[mandelbrot thread 2]:          [12.163] ms
[mandelbrot thread 5]:          [12.065] ms
[mandelbrot thread 6]:          [12.013] ms
[mandelbrot thread 7]:          [11.953] ms
[mandelbrot thread 3]:          [12.569] ms
[mandelbrot thread 8]:          [12.080] ms
[mandelbrot thread 9]:          [12.142] ms
[mandelbrot thread 12]:         [12.032] ms
[mandelbrot thread 14]:         [11.927] ms
[mandelbrot thread 4]:          [12.978] ms
[mandelbrot thread 11]:         [12.449] ms
[mandelbrot thread 13]:         [12.333] ms
[mandelbrot thread 16]:         [12.305] ms
[mandelbrot thread 17]:         [12.369] ms
[mandelbrot thread 19]:         [12.147] ms
[mandelbrot thread 22]:         [11.910] ms
[mandelbrot thread 23]:         [12.103] ms
[mandelbrot thread 10]:         [13.528] ms
[mandelbrot thread 26]:         [12.202] ms
[mandelbrot thread 25]:         [12.268] ms
[mandelbrot thread 20]:         [12.824] ms
[mandelbrot thread 29]:         [12.039] ms
[mandelbrot thread 24]:         [12.548] ms
[mandelbrot thread 30]:         [12.187] ms
[mandelbrot thread 21]:         [13.414] ms
[mandelbrot thread 27]:         [12.979] ms
[mandelbrot thread 0]:          [12.858] ms
[mandelbrot thread 18]:         [14.076] ms
[mandelbrot thread 28]:         [13.503] ms
[mandelbrot thread 15]:         [16.156] ms
[mandelbrot thread 31]:         [15.363] ms
[mandelbrot thread]:            [18.702] ms
[mandelbrot thread 2]:          [12.431] ms
[mandelbrot thread 4]:          [12.905] ms
[mandelbrot thread 8]:          [12.205] ms
[mandelbrot thread 5]:          [12.846] ms
[mandelbrot thread 9]:          [12.765] ms
[mandelbrot thread 7]:          [13.029] ms
[mandelbrot thread 6]:          [13.576] ms
[mandelbrot thread 1]:          [14.528] ms
[mandelbrot thread 12]:         [12.737] ms
[mandelbrot thread 19]:         [12.283] ms
[mandelbrot thread 13]:         [13.116] ms
[mandelbrot thread 27]:         [12.188] ms
[mandelbrot thread 14]:         [13.590] ms
[mandelbrot thread 20]:         [13.517] ms
[mandelbrot thread 10]:         [14.882] ms
[mandelbrot thread 11]:         [14.827] ms
[mandelbrot thread 29]:         [13.038] ms
[mandelbrot thread 17]:         [14.292] ms
[mandelbrot thread 16]:         [14.525] ms
[mandelbrot thread 21]:         [14.044] ms
[mandelbrot thread 22]:         [14.129] ms
[mandelbrot thread 26]:         [14.121] ms
[mandelbrot thread 18]:         [14.803] ms
[mandelbrot thread 25]:         [14.312] ms
[mandelbrot thread 31]:         [13.646] ms
[mandelbrot thread 24]:         [14.396] ms
[mandelbrot thread 15]:         [15.582] ms
[mandelbrot thread 28]:         [14.826] ms
[mandelbrot thread 23]:         [15.444] ms
[mandelbrot thread 0]:          [14.867] ms
[mandelbrot thread 30]:         [14.295] ms
[mandelbrot thread 3]:          [18.934] ms
[mandelbrot thread]:            [19.770] ms
[mandelbrot thread 3]:          [14.837] ms
[mandelbrot thread 6]:          [14.533] ms
[mandelbrot thread 8]:          [14.368] ms
[mandelbrot thread 4]:          [15.215] ms
[mandelbrot thread 21]:         [13.048] ms
[mandelbrot thread 18]:         [13.930] ms
[mandelbrot thread 17]:         [14.101] ms
[mandelbrot thread 15]:         [14.441] ms
[mandelbrot thread 16]:         [14.510] ms
[mandelbrot thread 14]:         [14.851] ms
[mandelbrot thread 19]:         [14.335] ms
[mandelbrot thread 12]:         [15.520] ms
[mandelbrot thread 28]:         [13.634] ms
[mandelbrot thread 11]:         [16.110] ms
[mandelbrot thread 30]:         [13.774] ms
[mandelbrot thread 29]:         [13.781] ms
[mandelbrot thread 23]:         [14.942] ms
[mandelbrot thread 27]:         [14.492] ms
[mandelbrot thread 1]:          [18.225] ms
[mandelbrot thread 7]:          [17.628] ms
[mandelbrot thread 0]:          [14.576] ms
[mandelbrot thread 2]:          [18.368] ms
[mandelbrot thread 25]:         [15.301] ms
[mandelbrot thread 24]:         [15.180] ms
[mandelbrot thread 9]:          [17.818] ms
[mandelbrot thread 10]:         [17.685] ms
[mandelbrot thread 13]:         [17.464] ms
[mandelbrot thread 26]:         [15.757] ms
[mandelbrot thread 22]:         [16.542] ms
[mandelbrot thread 5]:          [21.269] ms
[mandelbrot thread 31]:         [15.387] ms
[mandelbrot thread 20]:         [21.118] ms
[mandelbrot thread]:            [24.140] ms
[mandelbrot thread 2]:          [13.186] ms
[mandelbrot thread 1]:          [14.538] ms
[mandelbrot thread 3]:          [15.142] ms
[mandelbrot thread 4]:          [15.036] ms
[mandelbrot thread 19]:         [13.791] ms
[mandelbrot thread 7]:          [16.078] ms
[mandelbrot thread 17]:         [14.823] ms
[mandelbrot thread 27]:         [15.371] ms
[mandelbrot thread 12]:         [17.877] ms
[mandelbrot thread 11]:         [18.415] ms
[mandelbrot thread 6]:          [19.983] ms
[mandelbrot thread 5]:          [20.102] ms
[mandelbrot thread 15]:         [19.057] ms
[mandelbrot thread 22]:         [18.249] ms
[mandelbrot thread 9]:          [20.133] ms
[mandelbrot thread 8]:          [20.471] ms
[mandelbrot thread 28]:         [18.466] ms
[mandelbrot thread 10]:         [21.070] ms
[mandelbrot thread 18]:         [20.054] ms
[mandelbrot thread 20]:         [20.075] ms
[mandelbrot thread 13]:         [21.037] ms
[mandelbrot thread 31]:         [16.124] ms
[mandelbrot thread 21]:         [20.583] ms
[mandelbrot thread 16]:         [22.044] ms
[mandelbrot thread 14]:         [22.390] ms
[mandelbrot thread 26]:         [20.800] ms
[mandelbrot thread 0]:          [18.206] ms
[mandelbrot thread 23]:         [21.904] ms
[mandelbrot thread 25]:         [19.888] ms
[mandelbrot thread 30]:         [21.778] ms
[mandelbrot thread 29]:         [21.986] ms
[mandelbrot thread 24]:         [23.202] ms
[mandelbrot thread]:            [26.570] ms
[mandelbrot thread 1]:          [14.085] ms
[mandelbrot thread 4]:          [14.448] ms
[mandelbrot thread 8]:          [14.172] ms
[mandelbrot thread 5]:          [15.783] ms
[mandelbrot thread 12]:         [12.856] ms
[mandelbrot thread 3]:          [16.382] ms
[mandelbrot thread 2]:          [17.093] ms
[mandelbrot thread 10]:         [14.147] ms
[mandelbrot thread 13]:         [13.327] ms
[mandelbrot thread 17]:         [13.103] ms
[mandelbrot thread 6]:          [17.109] ms
[mandelbrot thread 7]:          [17.047] ms
[mandelbrot thread 14]:         [14.041] ms
[mandelbrot thread 9]:          [15.600] ms
[mandelbrot thread 20]:         [14.183] ms
[mandelbrot thread 11]:         [16.098] ms
[mandelbrot thread 25]:         [12.927] ms
[mandelbrot thread 24]:         [13.333] ms
[mandelbrot thread 19]:         [14.714] ms
[mandelbrot thread 18]:         [15.489] ms
[mandelbrot thread 27]:         [13.177] ms
[mandelbrot thread 15]:         [16.287] ms
[mandelbrot thread 0]:          [12.867] ms
[mandelbrot thread 22]:         [15.398] ms
[mandelbrot thread 29]:         [14.015] ms
[mandelbrot thread 30]:         [13.810] ms
[mandelbrot thread 16]:         [17.053] ms
[mandelbrot thread 28]:         [12.996] ms
[mandelbrot thread 21]:         [16.319] ms
[mandelbrot thread 31]:         [13.287] ms
[mandelbrot thread 23]:         [17.633] ms
[mandelbrot thread 26]:         [19.871] ms
[mandelbrot thread]:            [27.350] ms
[mandelbrot thread BEST]:               [18.702] ms
Wrote image file mandelbrot-thread.ppm
                                (19.42x speedup from 32 threads)


线程负责均衡性，加速比均有提升，现在删除计时函数重测t=8、16、32性能

t=8 6.72x

t=16 10.90x

t=32 16.05x


目前分配策略对于访存并不友好，下一步每个线程按CHUNK line进行分配，增加访存命中率。

t=8 6.92x(CHUNK=32) 

t=16 11.35x

t=32 13.66x (CHUNK=32)18.09x(CHUNK=16)






