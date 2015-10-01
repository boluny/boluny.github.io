---
layout: post
title: "python发邮件小结"
date: 2013-03-29 16:18 +0800
type: tech
---
python还是很强大的，同时函数库也多，实现一些功能也很方便，比如现在要讨论的这个发邮件功能。一般而言，电子邮件相关的协议主要是POP3，SMTP等，而python中提供了一个标准库smtplib用来实现简单的邮件收发，废话不多说，直接上干货：

    # _*_ coding: utf-8 _*_
    import smtplib
    from email.mime.text import MIMEText
    from email.mime.multipart import MIMEMultipart
    from email.mime.base import MIMEBase
    from email.encoders import encode_base64
    import os.path
    import os
    
    mailto = ['xxxx@gmail.com']
    #mailto用列表可以给多个收件人发，
    #但注意在msg['To']里是一个字符串的表达，因此要join成一个字符串
    mailhost = 'smtp.163.com'
    mailuser = 'xxxx'
    mailpawd = 'xxxx'
    mailsuffix = '163.com'

    def send_mail(to, sub, content, filename):
        me = mailuser + '@' + mailsuffix 
        main_msg = MIMEMultipart()

        # construct the plain text part in the mail and attach it
        text_msg = MIMEText(content)
        main_msg.attach(text_msg)

        # construct the attachment part
        atttype = 'application/octet-stream'
        maintype, subtype = atttype.split('/', 1)
        if os.access(filename, os.F_OK): # if file exists
            data = open(filename, 'rb')
            file_msg = MIMEBase(maintype, subtype) # attachment type
            file_msg.set_payload(data.read())     # fill in the file_msg
            data.close()
            encode_base64(file_msg)     # encoding

            basename = os.path.basename(filename) # get name without path
            file_msg.add_header('Content-Disposition','attachment', 
                    filename = basename)
            main_msg.attach(file_msg)

            main_msg['Subject'] = sub
            main_msg['From'] = me
            main_msg['To'] = ', '.join(to)
            try:
                s =smtplib.SMTP()
                s.connect(mailhost)
                s.login(mailuser, mailpawd)
                s.sendmail(me, to, main_msg.as_string())
                s.close()
                print 'send success'
            except Exception, e:
                print str(e)
                print 'send failed'

    if __name__ == '__main__':
        subject = raw_input('enter subject:')
        content = raw_input('enter content:')
        send_mail(mailto, subject, content,'pic.jpg')

嗯，以上差不多就是这样的功能啦，具体的各种解释代码注释里有，写的时候参照了网上别人的一些文章，过些时候放上链接。

——————————–你以为这么快就结束了的分割线———————————–

嗯，有的朋友们会说：“坑爹呐这是”，确实是略简单了一点。那么换种思路，如果不让使用SMTP协议呢？是不是感觉有点玩人？可是事实就是这样，至少我的环境就是这样，SMTP禁了，不安全。那么又有何办法呢？

好像没有办法了啊。等等，Outlook好像可以发外网啊，那么借助于Outlook是不是能做到同样发邮件的功能呢？

嗯，google没有负我，搜出来确实有方法可以解决，不过需要借助第三方函数库win32com，这个是pythonwin中自带的对win32 中COM组件的API的一层封装，刚想找一个定义，无奈google被墙，baidu搜出来的结果惨不忍睹，只好作罢。以后看情况补上。按我的理解就是根 据COM的调用方式提供在python中的实现，一般而言，需要对已有的COM组件扫描进行makepy操作然后才方便进行COM组件的调用。然后在 win32com.client.constants.*使用该COM定义的各种常量参数等。

具体的情况还是如以下代码来呈现吧：

    # -*- coding:utf-8 -*-
    
    #========================================================
    # This file mainly handler the mail sent function.
    # Attention, you need install win32com to drive, or it will crash.
    # Moreover, the Outlook security level must be lower.
    # In Tools->Trust Center->Programmatic Access->Never warn me ... Check it.
    # may other issues comes up out of my consideration. just feel free to  
    # contact me. mail to qwop369258@gmail.com
    #========================================================
    
    import win32com.client
    import time
    
    attachmentlist = [r'D:pic.jpg', r'D:pic2.jpg']
    recvlist = ['xxxxx@sap.com','xxxxxx@gmail.com']
    
    def sendmail(recvlist, attachmentlist, subject, content, iswithtime):
        s = win32com.client.Dispatch("Outlook.Application")
        ol = s.GetNamespace('MAPI')

        # test if outlook can be connected
        inbox = ol.GetDefaultFolder(win32com.client.constants.olFolderInbox)
        messages = inbox.Items
        # print 'numbers: %s' % messages.Count
        # somemail = messages.Item()
        # f = open('cc.html','w')
        # f.write(somemail.HTMLBody)
        # f.close()

        mail = s.CreateItem(win32com.client.constants.olMailItem)

        # Add recipients
        for recv in recvlist:
            mail.Recipients.Add(recv)

        # Add attachments
        for attachment in attachmentlist:
            mail.Attachments.Add(attachment)

        # Time stamp preparation
        now = time.localtime()
        str_date = time.strftime('%m/%d/%Y', now)
        str_time = time.strftime('%X', now)

        # Compose mail subject and content
        mail.Subject = subject
        if iswithtime:
            mail.Subject = mail.Subject + ' in ' + str_date

        mail.Body = content
        if iswithtime:
            mail.Body = mail.Body + 'rngenerated at ' + str_date + ' ' + str_time

        mail.Send()

    if __name__ == '__main__':
        sendmail(recvlist, attachmentlist, 'test7', 'test', False)

主要还是以下的格式：

    x = win32com.client.Dispatch("some COM")
    x.property = yyyyy
    x.method(args)

python很简单，这个功能又简单，应该看一下就知道意思了，也不详细说了，不懂的可以留言问我

需要注意的一点是，在调用Outlook时会有一个安全性问题，就是外部调用它的API发邮件会有警告，还是要手动点allow或者deny，有一个方法可以去除掉相关的警告，在第二段代码开头注释有相关的操作说明。

再次，第二种方法感觉并不优雅，不知道有什么更好的方法可以实现功能，还请围观群众不吝赐教。

