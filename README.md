# KMLParser
A simple parser to read KML files in Android and use it on Google Maps


## Usage (TL;DR / I already know what I'm here for)
Simply copy the AsyncTask (PaintBorder) and modify it as you want. 

```
private class PaintBorder extends AsyncTask<String, Void, Void> {

        Vector<Polyline> lines;
        Vector<PolylineOptions> border;
        Vector<Vector<LatLng>> border_fragment;

        @Override
        protected void onPreExecute() {
            super.onPreExecute();
            lines = new Vector<>();
            border = new Vector<>();
            border_fragment = new Vector<>();
        }

        @Override
        protected Void doInBackground(String... params) {
            try {
                InputStream inputStream = getResources().openRawResource(Integer.parseInt(params[0]));
                DocumentBuilder docBuilder = DocumentBuilderFactory.newInstance().newDocumentBuilder();
                Document document = docBuilder.parse(inputStream);
                NodeList coordList;

                if (document == null) return null;

                coordList = document.getElementsByTagName("coordinates");

                for (int i = 0; i < coordList.getLength(); i++) {
                    String[] coordinatePairs = coordList.item(i).getFirstChild().getNodeValue().trim().split(" ");
                    Vector<LatLng> positions = new Vector<>();
                    for (String coord : coordinatePairs) {
                        positions.add(new LatLng(Double.parseDouble(coord.split(",")[1]), 
                                                 Double.parseDouble(coord.split(",")[0])));
                    }
                    border_fragment.add(positions);
                }
            } catch (ParserConfigurationException | IOException | SAXException e) {
                e.printStackTrace();
            }
            return null;
        }

        @Override
        protected void onPostExecute(Void result) {
            super.onPostExecute(result);

            int i;
            for (i = 0; i < border_fragment.size(); i++) {
                PolylineOptions dots = new PolylineOptions();
                for (int j = 0; j < border_fragment.get(i).size(); j++) dots.add(border_fragment.get(i).get(j));
                border.add(dots);
            }

            for (i = 0; i < border.size(); i++) lines.add(map.addPolyline(border.get(i)));

            for (i = 0; i < lines.size(); i++) {
                lines.get(i).setWidth(2);
                lines.get(i).setColor(Color.RED);
                lines.get(i).setGeodesic(false);
                lines.get(i).setVisible(true);
            }
        }
}
```

## Usage (explain!!!)