# funixlearn huong dan su dung file "lastname firstname grade the exam"
import pandas as pd
import numpy as np
filename = 0
while filename != "stop": #sử dụng vòng lặp để chương trình chạy xuyên suốt các file sẽ dừng khi nhập stop
    filename = input("Enter a filename (enter 'stop' to stop program): ")
    try: #dùng try except để thông báo tên file cần đọc không tồn tại trong folder
        f = open("Data Files\\"+filename + ".txt")
        print("Successfully opened "+filename + ".txt")
        print("**** ANALYZING ****")
        data = pd.DataFrame(f)
        data = data[0].apply(lambda x: x.split(","))
        n = data.count()
        i = 0
        valid_data = 0
        invalid_data = 0
        answer_key = "B,A,D,D,C,B,D,A,C,C,D,B,A,B,A,C,B,D,A,C,A,A,B,D,D"
        answer_key = answer_key.split(",")# chuyển chuỗi đáp án thành list
        mark_table = []# biến để tạo mảng bảng điểm
        miss_table = []# biến để tạo mảng đếm câu bị bỏ trống
        wrong_table = []# biến để đếm câu sai
        mark_list = ""
        for i in range (n): # vòng lặp for với số dòng dữ liệu trong file để tạo các chuỗi và mảng numpy cần thiết cho tính toán kiểm tra và tính điểm dữ liệu
            mark = []
            missing = [0]
            wrong = [0]
            test = data.iloc[i]
            test[-1] = test[-1][0]
            if len(test) == 26 and len(test[0])==9 and test[0][0] == "N" and test[0][1:].isnumeric() == True: # tạo lệnh if để xác định dòng kết quả hợp lệ
                code = test[1:]
                for a in range(len(answer_key)):
                    if code[a] == answer_key[a]:
                        mark.append(4)
                        missing.append(0)
                        wrong.append(0)
                    elif code[a] == "\n" or code[a] == "":
                        mark.append(0)
                        missing.append(1)
                        wrong.append(0)
                    else:
                        mark.append(-1)
                        missing.append(0)
                        wrong.append(1)
                mark_table.append(mark)
                miss_table.append(missing)
                wrong_table.append(wrong)
                mark_list = mark_list+ test[0]+","+ str(np.sum(mark))
                if i < n-1: #xóa dòng trống cuối cùng
                    mark_list = mark_list + "\n"
                valid_data = valid_data+1
            elif len(test) != 26:
                print("Invalid line of data: does not contain exactly 26 values:")
                print(test)
                invalid_data = invalid_data+1
            elif len(test[0])!=9 or test[0][0] != "N" or test[0][1:].isnumeric() != True:
                print("Invalid line of data: N# is invalid")
                print(test)
                invalid_data = invalid_data+1
            i = i+1
        if invalid_data == 0:
            print("No errors found!")
        print("**** REPORT ****")
        print("Total valid lines of data:", valid_data)
        print("Total invalid lines of data:", invalid_data)
        mark_table1 = np.sum(mark_table, axis = 1)
        miss_table1 = np.sum(miss_table, axis = 0)
        wrong_table1 = np.sum(wrong_table, axis = 0)
        max_missing = np.max(miss_table1)
        max_wrong = np.max(wrong_table1)
        missing_index = str(np.where(miss_table1 == max_missing))
        wrong_index = str(np.where(wrong_table1 == max_wrong))
        missing_percent = max_missing/n
        wrong_percent = max_wrong/n
        up = (np.array(mark_table1) > 80)
        print("Total student of high scores:",len(np.array(mark_table1)[up]))
        print("Mean (average) score:",np.round(np.mean(mark_table1), 3))
        print("Highest score:",np.max(mark_table1))
        print("Lowest score:",np.min(mark_table1))
        print("Range of scores:",np.max(mark_table1)-np.min(mark_table1))
        print("Median score:",np.round(np.median(mark_table1),3))
        print("Question that most people skip: missing_index -",missing_index," max_missing -",max_missing," missing percent - ",missing_percent)
        print("Question that most people answer incorrectly: wrong_index - ",wrong_index," max_wrong - ",max_wrong, " wrong_percent - ",wrong_percent)
        f = open("Data Files\\"+filename + "_grades.txt", "a")
        f.write(mark_list)
        f.close()
        f = open("Data Files\\"+filename + "_grades.txt", "r")
        print("# this is what "+filename + "_grades.txt should look like")
        print(f.read())
    except:
        print("File cannot be found")
