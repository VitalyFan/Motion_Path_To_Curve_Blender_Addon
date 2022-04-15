## Motion_Path_To_Curve_Blender_Addon
### Blender Аддон для преобразования Motion Paths в Curves [Аддон](https://github.com/VitalyFan/Motion_Path_To_Curve_Blender_Addon/blob/main/AddonMP.py)
Аддон позволяет преобразовать Motion Paths в кривые и сделать их пригодными для дальнейшего редактирования
<hr/>
Краткая инструкция по установке:

- Установка производится стандратным способом: Edit -> Preferences
<img src ="https://user-images.githubusercontent.com/78021829/162607261-7abc1c65-21c9-4055-a91e-517e8c1abf13.png" width="300"/>

- Add-ons -> Install...
<img src ="https://user-images.githubusercontent.com/78021829/162607315-267bcddf-b230-4d5d-8de0-beb7fc3d4ee8.png" width="500"/>

- Выбор скаченного файла
<img src ="https://user-images.githubusercontent.com/78021829/162607787-df79d6fa-b8ec-4791-a94e-01d2ffd572e3.jpg" width="700"/>

***
Активировать Аддон можно на клавишу F3

<img src ="https://user-images.githubusercontent.com/78021829/162607918-441f1828-abdb-4fb2-a03b-f6d315280414.png" width="700"/>
***

    def image_callback_color(data):
        global color, figure

        cv_image = bridge.imgmsg_to_cv2(data, 'bgr8') # OpenCV image
        img_hsv = cv2.cvtColor(cv_image, cv2.COLOR_BGR2HSV) #[118:119,158:159]

        #detected color
        #print(img_hsv[0][0])
        red_mask1 = cv2.inRange(img_hsv, (0,210,210), (0, 216, 255))
        red_mask2 = cv2.inRange(img_hsv, (172, 110, 150), (180, 255, 255))
        red_mask = cv2.bitwise_or(red_mask1,red_mask2)
        if red_mask[119][159] == 255:
            color = 'red'
        cnt_red, hierarchy = cv2.findContours(red_mask, cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_SIMPLE)
        cv2.drawContours(cv_image, cnt_red, -1, (255,0,0), 1, cv2.LINE_AA, hierarchy, 1 )

        for c in cnt_red:
            perimeter = cv2.arcLength(c,True)
            approx = cv2.approxPolyDP(c,perimeter*0.01,True)
            # print(approx)
            # print(len(approx))

            if len(approx) == 4:
                (x, y, w, h) = cv2.boundingRect(approx)
                ar = w / float(h)
                # print(ar)
                if ar <= 1.10 and ar >= 0.90:
                    figure = 'square'
                else:
                    figure = 'rectangle'

            if len(approx) == 10:
                figure = 'star'
