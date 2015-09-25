# -*- coding: utf-8 -*-
"""
Created on Wed Sep 23 17:02:08 2015
@brief:     this function provides a method to scrawl your google scholar profile   
@author:    Qiang Zhang
@email:     zhangtemplar@gmail.com
@license:   You are free to use or to change it, as long as you include my name and email there.
"""

import urllib2
from bs4 import BeautifulSoup
# you can change Ie to other browser, however remember to install webkit driver accordingly
from selenium.webdriver import Ie as Browser

# url of google scholar
url_base='https://scholar.google.com'
# verbose level to get list of publications
verbose_publication_list=1
# verbose level to get the citation list for eah of your publication
verbose_citation_list=2

def extract_scholar(profile_url, verbose=verbose_citation_list):
    """
    main function of extract scholar
    @param[in]      profile_url     the link of google scholar profile you want to crawl
    @param[in]      verbose         the level of information you want to scrawl. By default, we will scraw the detailed citation list for each of your publicaiton
    @return         the profile information as a dictionary
    """
    scholar_info={}
    # get the page contents
    req=urllib2.Request(profile_url, headers={'User-Agent': 'Mozilla/5.0 (X11; Linux x86_64; rv:27.0) Gecko/20100101 Firefox/27.0'})
    p=urllib2.urlopen(req)
    
    # parse the page
    soup=BeautifulSoup(p.readlines()[0], 'html.parser')
    
    # scholar profile picture
    s=soup.find(id='gsc_prf_pup')
    scholar_info['picture']=url_base+s.attrs['src']
    
    # scholar name
    s=soup.find(id='gsc_prf_in')
    scholar_info['name']=s.contents
    
    # scholar's other information, e.g., university
    
    # scholar's email address
    # s=soup.find(id='gsc_prf_i')
    # scholar_info['homepage']=s.contents[1].attrs['href']
    
    # scholar's citation, h-index, h-10
    s=soup.find(id='gsc_rsb_st')
    scholar_info['citation']=int(s.contents[1].find_all('td')[1].contents[0])
    scholar_info['citation_since_2010']=int(s.contents[1].find_all('td')[2].contents[0])
    scholar_info['h_index']=int(s.contents[2].find_all('td')[1].contents[0])
    scholar_info['h_index_since_2010']=int(s.contents[2].find_all('td')[2].contents[0])
    scholar_info['h10']=int(s.contents[3].find_all('td')[1].contents[0])
    scholar_info['h10_2010']=int(s.contents[3].find_all('td')[2].contents[0])
    
    # scholar's per-year citation
    s=soup.find(id='gsc_g_x')
    year=[]
    for y in s.contents:
        year.append(int(y.contents[0]))
        s=soup.find(id='gsc_g_bars')
    citation=[]
    for c in s.contents:
        citation.append(int(c.contents[0].contents[0]))
    citation_by_year=dict(zip(year,citation))
    scholar_info['citation_by_year']=citation_by_year
    
    # publication list
    if verbose>=verbose_publication_list:
        print 'getting the publication list, this may take some time'
        scholar_info['publication']=extract_publication(profile_url, verbose)
    return scholar_info
    
def extract_publication(profile_url, verbose=verbose_citation_list):
    """
    this function crawl the publication list from the google scholar profile
    @param[in]      profile_url     the link of google scholar profile you want to crawl
    @param[in]      verbose         the level of information you want to scrawl. By default, we will scraw the detailed citation list for each of your publicaiton
    @return         the list of pulication as a list, where each entry is a dictionary
    """
    # scholar's artical list
    browser=Browser()
    browser.get(profile_url)
    publication_list=browser.find_elements_by_class_name('gsc_a_tr')
    publication={}
    while True:
        for publication_item in publication_list:
            title=publication_item.find_element_by_class_name('gsc_a_at').text
            print title,
            author=publication_item.find_elements_by_class_name('gs_gray')[0].text.split(', ')
            vendor=publication_item.find_elements_by_class_name('gs_gray')[1].text
            try:
                citation=int(publication_item.find_element_by_class_name('gsc_a_ac').text)
                link=publication_item.find_element_by_class_name('gsc_a_ac').get_attribute('href')
            except:
                citation=0
                link=None
            year=int(publication_item.find_element_by_class_name('gsc_a_h').text)
            if citation>0 and verbose>=verbose_citation_list:
                print 'and its citation list',
                cited_by=extract_citation_for_publication(link)
            else:
                cited_by=None    
            print 'finished'
            publication[title]={'link':link,'author':author,'vendor':vendor,'citation':citation, 'cited by': cited_by, 'year':year}
        if not next_page(browser):
            break
    browser.close()
    return publication
        
def extract_citation_for_publication(link):
    """
    this function craws the list of articles from a given link. If it has next page, it will continue to it until there is none
    @param[in]      profile_url     the link of google scholar profile you want to crawl
    @return         the list of articles as a list where each entry is dictionary    
    """
    browser=Browser()
    citation={}
    # go the citation view
    # as the page is written is javascript, we are not able to get its content via urllib2
    # intead we will use Selenium to simulate a web browser to render the page
    # req=urllib2.Request(publication[k]['link'], headers={'User-Agent': 'Mozilla/5.0 (X11; Linux x86_64; rv:27.0) Gecko/20100101 Firefox/27.0'})
    # p=urllib2.urlopen(req)
    # sub_soup=BeautifulSoup(p.readlines()[0], 'html.parser')
    # s=sub_soup.find(id='gs_ccl')
    browser.get(link)
    while True:
        citation_root=browser.find_element_by_id('gs_ccl')
        citation_list=citation_root.find_elements_by_class_name('gs_r')
        for citation_item in citation_list:
            # title
            title=citation_item.find_element_by_class_name('gs_rt').text
            # try to get the downloading link, if there is one
            try:
                link=citation_item.find_element_by_id('gs_ggsW2')
                link=link.find_element_by_link_text(link.text).get_attribute('href')
            except:
                link=None
            # author
            author_line=citation_item.find_element_by_class_name('gs_a')
            author_name=author_line.text.split(', ')
            author={}
            # for each of the author, find its link if its exits
            for a in author_name:
                try:
                    print '.',
                    # there is a google scholar profile with author
                    item=author_line.find_element_by_link_text(a)
                    author[a]=item.get_attribute('href')
                except:
                    # there is not such profile
                    author[a]=None
            # we can also press the cite button to get the detailed citation information, skipped here
            citation[title]={'link':link, 'author': author}
        # go to next page, if there is one
        if not next_page(browser):
            break
    # close
    browser.close()
    return citation
    
# click the next button, if there is one
def next_page(browser):
    try:
        navigation=browser.find_element_by_id('gs_n')
        navigation_next=navigation.find_element_by_link_text('Next')  
        browser.get(navigation_next.get_attribute('href'))
        return True
    except:
        try:
            # try button instaed
            navigation=browser.find_element_by_id('gsc_bpf_next')
            navigation.click()
        except:
            # there is one next
            return False
        
if __name__ is '__main__':
    print extract_scholar('https://scholar.google.com/citations?user=88s55KAAAAAJ&hl=en')
