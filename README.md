# I-am-a-Data-Scientist-
##This assignment requires that you to find at least two datasets on the web which are related, and that you visualize these datasets to answer a question with the broad topic of economic activity or measures (see below) for the region of Ann Arbor, Michigan, United States, or United States more broadly.  You can merge these datasets with data from different regions if you like! For instance, you might want to compare Ann Arbor, Michigan, United States to Ann Arbor, USA. In that case at least one source file must be about Ann Arbor, Michigan, United States.  You are welcome to choose datasets at your discretion, but keep in mind they will be shared with your peers, so choose appropriate datasets. Sensitive, confidential, illicit, and proprietary materials are not good choices for datasets for this assignment. You are welcome to upload datasets of your own as well, and link to them using a third party repository such as github, bitbucket, pastebin, etc. Please be aware of the Coursera terms of service with respect to intellectual property.  Also, you are welcome to preserve data in its original language, but for the purposes of grading you should provide english translations. You are welcome to provide multiple visuals in different languages if you would like!  As this assignment is for the whole course, you must incorporate principles discussed in the first week, such as having as high data-ink ratio (Tufte) and aligning with Cairo’s principles of truth, beauty, function, and insight.  Here are the assignment instructions:  State the region and the domain category that your data sets are about (e.g., Ann Arbor, Michigan, United States and economic activity or measures). You must state a question about the domain category and region that you identified as being interesting. You must provide at least two links to available datasets. These could be links to files such as CSV or Excel files, or links to websites which might have data in tabular form, such as Wikipedia pages. You must upload an image which addresses the research question you stated. In addition to addressing the question, this visual should follow Cairo's principles of truthfulness, functionality, beauty, and insightfulness. You must contribute a short (1-2 paragraph) written justification of how your visualization addresses your stated research question. What do we mean by economic activity or measures? For this category you might look at the inputs or outputs to the given economy, or major changes in the economy compared to other regions.


import matplotlib.pyplot as plt
import pandas as pd
import numpy as np


df1 = pd.read_excel('https://studentaid.ed.gov/sa/sites/default/files/fsawg/datacenter/library/DLbyForbearanceType.xls') #Forbearance Type
df2 = pd.read_excel('https://studentaid.ed.gov/sa/sites/default/files/fsawg/datacenter/library/DLbyDefermentType.xls') #Deferment Type


#cleanup dlbyfb spreadsheet
df1=df1[5:22] #remove extraennous info
df1.rename(columns={'Direct Loan Portfolio by Forbearance Type':'Year'}, inplace=True) #change the column title of the Year to 'Year'
df1=df1.set_index(['Year','Unnamed: 1']) #set the year and quarter as the index
df1=df1['Unnamed: 10'] #secluded the Unreported Group
df1=df1.reset_index()
prefcol=df1['Year']

#cleanup dlbydf spreadsheet
df2=df2[5:22]
df2.rename(columns={'Direct Loan Portfolio by Deferment Type':'Year'}, inplace=True)
df2=df2.set_index(['Year','Unnamed: 1'])['Unnamed: 14']
df2=df2.reset_index()
df2['Year']=prefcol


df1=df1.set_index(['Year','Unnamed: 1']) #set the year and quarter as the index
df1=df1['Unnamed: 10'] #secluded the Unreported Group
df2=df2.set_index(['Year','Unnamed: 1'])['Unnamed: 14']

#convert series to dataframe
df1=df1.to_frame()
df2=df2.to_frame()

dfnew=pd.merge(df1,df2,left_index=True, right_index=True)
dfnew=dfnew.reset_index()
dfnew["Period"]=dfnew['Year'].map(str)+dfnew['Unnamed: 1']
dfnew=dfnew[['Period','Unnamed: 10','Unnamed: 14']].set_index('Period')


fbmoney=tuple(dfnew['Unnamed: 10'].tolist()[1:])
dfmoney=tuple(dfnew['Unnamed: 14'].tolist()[1:])


x_axis=tuple(dfnew.index.tolist()[1:])
y_pos=np.arange(len(x_axis))


#plot
plt.plot(fbmoney, color='green', linestyle='dashed',linewidth=4, marker='o',markerfacecolor='blue', markersize=8, label='Forbearance Type')
plt.plot(dfmoney, color='red', linestyle='dashed',linewidth=4, marker='*',markerfacecolor='black', markersize=12, label='Deferment Type')

plt.xlabel('Period')
plt.ylabel('Dollars Outstanding - in Billions')
plt.title('Unreported U.S. National Student Direct Loan Portfolio')

plt.xticks(y_pos+0.35/2, x_axis)
plt.xticks(rotation=90)
plt.yticks(np.arange(0,12.5,1))
plt.legend(loc='best')
plt.show()
