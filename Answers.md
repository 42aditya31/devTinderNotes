- What is the difference between Props and state:
--> So in React app we have to make small small component , they compoenent have to magae the data and to manage that data we use state , 
--> and props means something what we are passing to the Components 


- How you can create a react project:
--> there are multiple way to create a project 



--

const data = [
    "https://i.pinimg.com/736x/23/5e/09/235e09099e71c062df1aea0d2babd2a6.jpg",
    "https://wallpapers.com/images/hd/random-background-1920-x-1200-6ciewyud1u8r1xkl.jpg",
    "https://c4.wallpaperflare.com/wallpaper/92/785/218/random-recorder-yellow-hd-wallpaper-preview.jpg",
    "https://i.pinimg.com/736x/23/5e/09/235e09099e71c062df1aea0d2babd2a6.jpg",
    "https://wallpapers.com/images/hd/random-background-1920-x-1200-6ciewyud1u8r1xkl.jpg",
    "https://c4.wallpaperflare.com/wallpaper/92/785/218/random-recorder-yellow-hd-wallpaper-preview.jpg",
]

const ImageSlider = () =>{

   const [activeIndex, setactiveIndex] = useState(0);

   const handlePrevious = () => {
        setActiveIndex(activeIndex === 0 ? data.length - 1 : activeIndex - 1);
    };

    const handleNext = () => {
        setActiveIndex(activeIndex === data.length - 1 ? 0 : activeIndex + 1);
    };

    return <div>
    <button onclick={handleprevious}>Previous</button>
   {map.data((url,i)=><img src={url} className=("extra classese"+ {activeIndex === i ? "block" : "hidden"}) />)} 
    <button onclick={handlenext}>Next</button>
    </div>
}

export default ;