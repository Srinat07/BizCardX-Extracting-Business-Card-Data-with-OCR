# BizCardX-Extracting-Business-Card-Data-with-OCR Capstone project -3
BizCardX-Extracting-Business-Card-Data-with-OCR  (GUVI  PROJECT)
# What is BizCard ?
   
BizCardX: Extracting Business Card Data with OCR Overview BizCardX is a Streamlit web application which extracts data from business cards using Optical Character Recognition (OCR). Users can upload an image of a business card and the application uses the easyOCR library to extract relevant information from the card. The extracted information is then displayed in a user-friendly format and can be stored in a MySQL database for future reference
    
# What is OCR (Optical Character Recognition)?
# Optical Character Recognition (OCR) 
Is the process that converts an image of text into a machine-readable text format. For example, if you scan a form or a receipt, your computer saves the scan as an image file. You cannot use a text editor to edit, search, or count the words in the image file. However, you can use OCR to convert the image into a text document with its contents stored as text data.

# Why is OCR important?
Most business workflows involve receiving information from print media. Paper forms, invoices, scanned legal documents, and printed contracts are all part of business processes. These large volumes of paperwork take a lot of time and space to store and manage. Though paperless document management is the way to go, scanning the document into an image creates challenges. The process requires manual intervention and can be tedious and slow.

Moreover, digitizing this document content creates image files with the text hidden within it. Text in images cannot be processed by word processing software in the same way as text documents. OCR technology solves the problem by converting text images into text data that can be analyzed by other business software. You can then use the data to conduct analytics, streamline operations, automate processes, and improve productivity.

# Image Pre-Processing
As I mentioned before about the difficulties of extracting text from a business card image due to the card design. Here the cv2 used to create a rectangular bounding box over the extracted text information and label them.

# reader = easyocr.Reader(['en'])
we initialize the easy OCR using ‘reader’ where the parameter ‘en’ is for english

        def image_preview(image, res):
            for (bbox, text, prob) in res:
                # unpack the bounding box
                (tl, tr, br, bl) = bbox
                tl = (int(tl[0]), int(tl[1]))
                tr = (int(tr[0]), int(tr[1]))
                br = (int(br[0]), int(br[1]))
                bl = (int(bl[0]), int(bl[1]))
                cv2.rectangle(image, tl, br, (0, 255, 0), 2)
                cv2.putText(image, text, (tl[0], tl[1] - 10),
                            cv2.FONT_HERSHEY_SIMPLEX, 0.7, (255, 0, 0), 2)
            plt.rcParams['figure.figsize'] = (15, 15)
            plt.axis('off')
            plt.imshow(image)

I created a function to get Full name, email , phone number, address, Website and so on. Using Regular expressions, I’ve fetched the text information from the business card.

        def get_data(res):
            for ind, i in enumerate(res):
                if "www " in i.lower() or "www." in i.lower():  # Website with 'www'
                    data["website"].append(i)
                elif "WWW" in i:  # In case the website is in the next elements of the 'res' list
                    website = res[ind + 1] + "." + res[ind + 2]
                    data["website"].append(website)
                elif '@' in i:
                    data["email"].append(i)
                # To get MOBILE NUMBER
                elif "-" in i:
                    data["mobile_number"].append(i)
                    if len(data["mobile_number"]) == 2:
                        data["mobile_number"] = " & ".join(data["mobile_number"])
                # To get COMPANY NAME
                elif ind == len(res) - 1:
                    data["company_name"].append(i)
                # To get Card Holder Name
                elif ind == 0:
                    data["card_holder"].append(i)
                #To get designation
                elif ind == 1:
                    data["designation"].append(i)

                #To get area
                if re.findall('^[0-9].+, [a-zA-Z]',i):
                    data["area"].append(i.split(',')[0])
                elif re.findall('[0-9] [a-zA-z]+',i):
                    data["area"].append(i)
                #To get city name
                match1 = re.findall('.+St , ([a-zA-Z]+).+',i)
                match2 = re.findall('.+St,,([a-zA-Z]+).+',i)
                match3 = re.findall('^[E].*',i)
                if match1:
                    data["city"].append(match1[0])
                elif match2:
                    data["city"].append(match2[0])
                elif match3:
                    data["city"].append(match3[0])

                #To get state name
                state_match = re.findall('[a-zA-Z]{9} +[0-9]', i)
                if state_match:
                    data["state"].append(i[:9])
                elif re.findall('^[0-9].+, ([a-zA-Z]+);', i):
                    data["state"].append(i.split()[-1])
                if len(data["state"]) == 2:
                    data["state"].pop(0)

                #To get Pincode
                if len(i) >= 6 and i.isdigit():
                    data["pin_code"].append(i)
                elif re.findall('[a-zA-Z]{9} +[0-9]', i):
                    data["pin_code"].append(i[10:])
        get_data(result)
Once an image is uploaded, BizCardX undertakes the image processing using the easyOCR library to extract essential details from the card. The extracted information encompasses:

Company name Card holder’s name Designation Mobile number Email address Website URL Area City State Pin code Image of the card.

