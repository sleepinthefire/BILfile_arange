# BILfile_arange
#BIL file getting with specimIQ, one of the hyper spectrum camera, converts it shape into 3D data cube for hyper spectrum data analysis. 

import numpy as np


"""
#データキューブを一次元で取得しBILの意味ある二次元配列に並べ替える
raw_data = np.fromfile('F:/back ups/lab/kashima/12_4_25m/2019-12-04_002/results/REFLECTANCE_2019-12-04_002.dat', dtype = 'float32')
BIL_data = raw_data.reshape(512,204*512)

#各レイヤーに分けて二次元配列をCSVとして保存
for x in range(204):
    layer_x = BIL_data[:512,x*512:(x+1)*512]
    np.savetxt('C:/Users/new_-/Desktop/separator/layer' + str(x) + '.csv', layer_x, delimiter = ',')
"""


#BILデータのglid length × glid lengthマスごとの平均をとってsave_folderにcsvを保存
def mean_csv(BIL_data, save_folder, nLayer=204, pixel_size=512, glid_length=32):
    number_of_glid = pixel_size // glid_length
    
    def mean(layer, row, column):
        glid_mean = np.mean(BIL_data[(glid_length*row) : glid_length*(row+1), (layer*pixel_size) + glid_length * column : (layer*pixel_size) + glid_length * (column+1)])
        return glid_mean
    
    
    for row in range(number_of_glid):
        for column in range(number_of_glid):
            mean_list = []
            for layer in range(nLayer):
                mean_list.append(mean(layer,row,column))
            mean_np = np.array(mean_list)
            np.savetxt(save_folder + '/mean' + str(row) + '_' + str(column) + '.csv', mean_np, delimiter=',')


"""
for x in range(0,16):
        for y in range(0,16):
            csv = np.loadtxt('D:/hyper_spectrum/2019-02-13/CSV/2019-02-13_083' + '/mean' + str(x) + '_' + str(y)  +'.csv' , delimiter=',').reshape(1,204)
    
        np.savetxt('D:/hyper_spectrum/2019-02-13/CSV/2019-02-13_083' + '/t_mean' + str(x) + '_' + str(y) + '.csv', csv, delimiter=',')
"""


#extract overwrapped mean from BIL data and save as csv. slide の分だけ移動していく　デフォルトではぐりっとの大きさは３２＊３２で16ピクセルごとに移動していく
def mean_csv_slide(BIL_data, save_folder, nLayer=204, pixel_size=512, glid_length=32, slide=16):

    def mean(layer, x, y):
        glid_mean = np.mean(BIL_data[ slide*y : slide*y + glid_length, ( layer * pixel_size ) + slide*x : (layer*pixel_size) + slide*x + glid_length ])
        return glid_mean
    
    for row in range((512 - glid_length)//slide):
        for column in range((512 - glid_length)//slide):
            mean_list = []
            for layer in range(nLayer):
                mean_list.append(mean(layer,row,column))
            mean_np = np.array(mean_list)
            np.savetxt(save_folder + '/mean' + str(row) + '_' + str(column) + '.csv', mean_np, delimiter=',')


"""          
raw_data = np.fromfile('D:/hyper_spectrum/2019-02-18_010/results/REFLECTANCE_2019-02-18_010.dat', dtype = 'float32')
BIL_data = raw_data.reshape(512,204*512)
save_folder = 'D:/hyper_spectrum/2019-02-18_010/results/CSV'

mean_csv_slide(BIL_data,save_folder,glid_length=128)
"""
