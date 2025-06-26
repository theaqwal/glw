multiple_results = []
for idx in range(len(data), -1, -1):

    #read the rawdata parameter from the returned object
    rawdata = data.rawdata.tolist()[idx-1].read()
    
    # read the binary part and assign it to b
    b = rawdata
    
    # get some header information
    header = get_header(b)
    
    # create a RawdataFile (rf) object and update its header
    rf = RawdataFile()
    rf.updateheader()
    
    
    # Store Wavelength/fiberend combination
    trace_data_keys = []
    trace_params_keys = []
    
    # predefine some arrays using the header information that got stored in rf
    trace_data_arr = np.zeros((rf.max_trace_pts, rf.n_traces))
    trace_data_arr = [[]]*rf.max_trace_pts*rf.n_traces
    trace_params_arr = np.zeros((100, rf.n_traces))
    trace_params_arr = [[]]*100*rf.n_traces
    
    # parse the number of traces
    for nt in range(rf.n_traces):
        
        X = ""
        for n in range(5):
            X += chr(b[64+nt*5 + n])
        trace_data_keys.append(X)
        trace_data_arr[nt][0] = X
        trace_params_arr[nt][0] = X
        trace_params_keys.append(X)
        
        # Store trace datapoints and parameters
        trace_data = {}
        
        
        trace_params = {}
        params_found = {}
        finished_trace = {}
        stored_param = {}
        total_trace_data_pts = {}
        
        for k in trace_data_keys:
            trace_data[k] = []
            trace_params[k] = []
            
            finished_trace[k] = True
            params_found[k] = False
            stored_param[k] = False
            
            total_trace_data_pts[k] = []
            
        whatever = []
        
        
        #Store trace datapoints and parameters
        
        for nt, k in enumerate(trace_data_keys):
            for n in range( rf.max_trace_pts):
                
                #       print(n, nt, k)
                
                #Fetch current point n for current trace nT if we have not already fetched all the data for this trace
                if finished_trace[k]:
                
                    ptr = rf.b_ptr + (nt) * rf.max_trace_pts*2 + (n)*2
                    X = b[ptr] + b[ptr + 1] * 256
                    if X >= 32768: X = 65536
                    whatever.append(X/rf.conv_factor)
                    
                data_traces = [pd.DataFrame(x) for x in np.split(np.array(whatever), T, if n_traces)]
                
                traces = {}
                for i, df in enumerate(data_traces):
                    df.columns = ['power_db']
                    df['length'] = [float(rf.point_spacing)] * np.arange(1, len(df)+1)
                    traces[trace_data_keys[i]] = df[['length', 'power_db']]
                    
                multiple_list.append([header, data, traces, rf])

def write_rawdata(filename, traces, rf ):

    header_rawdata = "## SM OTDR Bench #{} {}
## FIBER ID: {} Type : Option: Entered Len:{}
##RefractiveIndex =1.4643
##OTDR Serial # =" .format(rf.bench_num, rf.dt, rf.fiberid, rf.fiber_length)

    spacer_g_1550 = "##********************************************************************************
##RefractiveIndex =1.4643
##AvTime
##Wavelength=1550 FiberEnd=GreenEnd PulseWidth=100
##RefractiveIndex=1.4643 DBConversionFactor=1000 PI-Clip=0
##FP-O=-494.2167 PointSpacing={} NumberOfDataPoints={}
##--------------------------------------------------------------------------
##Point PkRaw PkRawDb Location
" .format(rf.point_spacing.strip(), rf.max_trace_pts-20)

    spacer_b_1550 = "##********************************************************************************
##RefractiveIndex =1.4643
##AvTime
##Wavelength=1550 FiberEnd=BrownEnd PulseWidth=100
##RefractiveIndex=1.4643 DBConversionFactor=1000 PI-Clip=0
##FP-O=-494.2167 PointSpacing={} NumberOfDataPoints={}
##--------------------------------------------------------------------------
##Point PkRaw PkRawDb Location
" .format(rf.point_spacing.strip(), rf.max_trace_pts-20)

    spacer_g_1310 = "##********************************************************************************
##RefractiveIndex =1.4643
##AvTime
##Wavelength=1310 FiberEnd=GreenEnd PulseWidth=100
##RefractiveIndex=1.4643 DBConversionFactor=1000 PI-Clip=0
##FP-O=-494.2167 PointSpacing={} NumberOfDataPoints={}
##--------------------------------------------------------------------------
##Point PkRaw PkRawDb Location
" .format(rf.point_spacing.strip(), rf.max_trace_pts-20)

    spacer_b_1310 = "##********************************************************************************
##RefractiveIndex =1.4643
##AvTime
##Wavelength=1310 FiberEnd=BrownEnd PulseWidth=100
##RefractiveIndex=1.4643 DBConversionFactor=1000 PI-Clip=0
##FP-O=-494.2167 PointSpacing={} NumberOfDataPoints={}
##--------------------------------------------------------------------------
##Point PkRaw PkRawDb Location
" .format(rf.point_spacing.strip(), rf.max_trace_pts-20)

    spacer_g_1410 = "##********************************************************************************
##RefractiveIndex =1.4643
##AvTime
##Wavelength=1410 FiberEnd=GreenEnd PulseWidth=100
##RefractiveIndex=1.4643 DBConversionFactor=1000 PI-Clip=0
##FP-O=-494.2167 PointSpacing={} NumberOfDataPoints={}
##--------------------------------------------------------------------------
##Point PkRaw PkRawDb Location
" .format(rf.point_spacing.strip(), rf.max_trace_pts-20)

    spacer_b_1410 = "##********************************************************************************
##RefractiveIndex =1.4643
##AvTime
##Wavelength=1410 FiberEnd=BrownEnd PulseWidth=100
##RefractiveIndex=1.4643 DBConversionFactor=1000 PI-Clip=0
##FP-O=-494.2167 PointSpacing={} NumberOfDataPoints={}
##--------------------------------------------------------------------------
##Point PkRaw PkRawDb Location
" .format(rf.point_spacing.strip(), rf.max_trace_pts-20)

    with open('{}.RAWDATA'.format(filename), 'w') as file:
        file.write(header_rawdata)
        file.write('\n')
        file.write(spacer_g_1550)
        file.write('\n')
        file.write(prepare_df(traces['1550G']))
        file.write(spacer_b_1550)
        file.write('\n')
        file.write(prepare_df(traces['1550B']))
        file.write(spacer_g_1310)
        file.write('\n')
        file.write(prepare_df(traces['1310G']))
        file.write(spacer_b_1310)
        file.write('\n')
        file.write(prepare_df(traces['1310B']))
        file.write(spacer_g_1410)
        file.write('\n')
        file.write(prepare_df(traces['1410G']))
        file.write(spacer_b_1410)
        file.write('\n')
        file.write(prepare_df(traces['1410B']))
        
    return None
